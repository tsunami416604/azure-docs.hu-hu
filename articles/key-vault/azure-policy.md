---
title: Azure Key Vault integrálása Azure Policy
description: Ismerje meg, hogyan integrálhatja a Azure Key Vaultt Azure Policy
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/28/2020
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: c32d2fb45bbd4f4a9b4845bb4ef27a439d536677
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905865"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Azure Key Vault integrálása Azure Policy

A [Azure Policy](../governance/policy/index.yml) egy irányítási eszköz, amely lehetővé teszi a felhasználók számára az Azure-környezetek nagy léptékű naplózását és kezelését. A Azure Policy lehetővé teszi az Azure-erőforrások guardrails elhelyezését annak biztosítása érdekében, hogy azok megfeleljenek a hozzárendelt szabályzati szabályoknak. Lehetővé teszi a felhasználók számára az Azure-környezet naplózásának, valós idejű kényszerítésének és szervizelésének elvégzését. A szabályzat által végrehajtott ellenőrzések eredményei elérhetők lesznek a felhasználók számára a megfelelőségi irányítópulton, ahol megtekinthetik, hogy mely erőforrások és összetevők megfelelőek, és melyek nem.  További információ: a [Azure Policy szolgáltatás áttekintése](../governance/policy/overview.md).

Példa használati forgatókönyvekre:

- Szeretné javítani a vállalata biztonsági állapotát azáltal, hogy bevezeti a vállalat kulcstartójában a minimális kulcs méretére és a tanúsítványok maximális érvényességi idejére vonatkozó követelményeket, de nem tudja, hogy mely csapatok lesznek megfelelőek, és melyek nem. 
- Jelenleg nincs megoldás a szervezeten belüli ellenőrzés elvégzésére, vagy a környezet kézi naplózására, ha a szervezeten belül egyéni csapatokat kér a megfelelőségük jelentéséhez. Ennek a feladatnak az automatizálására keres rá, valós időben végezheti el a naplózást, és garantálhatja a naplózás pontosságát.
- Szeretné kényszeríteni a vállalati biztonsági házirendeket, és állítsa le a magánszemélyeket önaláírt tanúsítványok létrehozásához, de nem rendelkezik automatikus módszerrel a létrehozásuk blokkolásához. 
- Szeretné kipróbálni néhány követelményt a tesztelési csapatoknak, de az éles környezetben is szigorú szabályozást szeretne fenntartani. Az erőforrások kényszerítésének elkülönítéséhez egyszerű automatizált módszerre van szükség. 
- Biztos lehet benne, hogy az új szabályzatok érvényesítését egy élő helyen lévő probléma esetén szeretné visszaállítani. A szabályzat kényszerítésének kikapcsolásához egykattintásos megoldásra van szükség. 
- Egy külső gyártótól származó megoldásra támaszkodik a környezet naplózására, és belső Microsoft-ajánlatot szeretne használni. 

## <a name="types-of-policy-effects-and-guidance"></a>A házirend hatásának és útmutatásának típusai

**Naplózás**: Ha egy házirend hatása naplózásra van beállítva, a házirend nem okoz változást a környezetében. Csak olyan összetevőkre figyelmeztet, amelyek nem felelnek meg a szabályzat-definícióknak egy adott hatókörön belül, ha ezeket az összetevőket nem megfelelőként jelöli meg a szabályzat megfelelőségi irányítópultján. A naplózás alapértelmezett, ha nincs kiválasztva házirend-effektus. 

**Megtagadás**: Ha egy házirend hatására a Megtagadás érték van beállítva, a házirend letiltja az új összetevők, például a tanúsítványok létrehozását, valamint a meglévő összetevők olyan új verzióinak letiltását, amelyek nem felelnek meg a házirend-definíciónak. A kulcstartón belüli meglévő, nem megfelelő erőforrások nem érintettek. A "naplózás" funkció továbbra is működni fog.

## <a name="available-built-in-policy-definitions"></a>Elérhető "beépített" szabályzat-definíciók

A Key Vault szabályzatokat hozott létre, amelyeket hozzárendelhet a tanúsítványok kezeléséhez használható gyakori forgatókönyvekhez. Ezek a szabályzatok beépítettek, ami azt jelenti, hogy nincs szükség egyéni JSON-írásra, hogy azok elérhetők legyenek, és a Azure Portal a hozzárendeléshez. Továbbra is testreszabhatja bizonyos paramétereket, hogy illeszkedjenek a szervezet igényeihez. 

A nyolc előnézeti szabályzat a következő.

### <a name="manage-certificate-validity-period-preview"></a>A tanúsítvány érvényességi idejének kezelése (előzetes verzió)

Ezzel a szabályzattal kezelheti a Key vaultban tárolt tanúsítványok maximális érvényességi időtartamát. Jó biztonsági gyakorlat a tanúsítványok maximális érvényességi időtartamának korlátozására. Ha a tanúsítvány titkos kulcsát az észlelés nélkül feltörték, a rövid életű tanúsítványok segítségével kis mértékben csökkentheti a folyamatos károsodások időbeli keretét, és csökkenti a tanúsítvány értékét egy támadó számára. 

### <a name="manage-allowed-certificate-key-types-preview"></a>Az engedélyezett tanúsítványok kulcs-típusainak kezelése (előzetes verzió)
Ez a szabályzat lehetővé teszi a kulcstartóban lévő tanúsítványok típusának korlátozását. Ennek a szabályzatnak a használatával meggyőződhet arról, hogy a tanúsítvány titkos kulcsa RSA, ECC vagy HSM-biztonsági másolat. A következő listából választhat, hogy milyen típusú tanúsítványok engedélyezettek.
- RSA
- RSA – HSM
- ECC 
- ECC – HSM 

### <a name="manage-certificate-lifetime-action-triggers-preview"></a>A tanúsítvány élettartamának kezelése művelet-eseményindítók (előzetes verzió)

Ez a szabályzat lehetővé teszi, hogy kezelje a lejáratuk meghatározott számú napjain vagy a felhasználható élettartamának bizonyos százalékában megadott élettartam-műveletet. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Integrált HITELESÍTÉSSZOLGÁLTATÓ által kiadott tanúsítványok kezelése (előzetes verzió)

Ha Key Vault integrált hitelesítésszolgáltatót (Digicert vagy GlobalSign) használ, és azt szeretné, hogy a felhasználók egy vagy több szolgáltatót használjanak, akkor ezt a házirendet használhatja a kijelölés naplózásához vagy érvényesítéséhez. Ez a szabályzat az önaláírt tanúsítványok a Key vaultban való létrehozásának naplózására és megtagadására is használható. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Integrált HITELESÍTÉSSZOLGÁLTATÓ által kiadott tanúsítványok kezelése (előzetes verzió)

Ha belső hitelesítésszolgáltatót vagy a Key Vault szolgáltatással nem integrált hitelesítésszolgáltatót használ, és azt szeretné, hogy a felhasználók a megadott listából hitelesítő szolgáltatót használjanak, ezt a házirendet használhatja a hitelesítésszolgáltatók engedélyezési listájának létrehozásához a kiállító neve alapján. Ez a szabályzat az önaláírt tanúsítványok a Key vaultban való létrehozásának naplózására és megtagadására is használható. 

### <a name="manage-allowed-curve-names-for-elliptic-curve-cryptography-certificates-preview"></a>Az engedélyezett görbék nevének kezelése az elliptikus görbe titkosítási tanúsítványainak számára (előzetes verzió)
Ha elliptikus görbe típusú titkosítást vagy ECC-tanúsítványokat használ, az alábbi listából testreszabhatja a görbe neveinek engedélyezett listáját. Az alapértelmezett beállítás lehetővé teszi az összes alábbi görbe nevét. 
- P-256
- P – 256K
- P-384
- P-521

### <a name="manage-minimum-key-size-for-rsa-certificates-preview"></a>RSA-tanúsítványok minimális kulcs-méretének kezelése (előzetes verzió)
Ha RSA-tanúsítványokat használ, kiválaszthatja, hogy a tanúsítványoknak milyen minimális méretűnek kell lennie. Az alábbi listából választhatja ki az egyik lehetőséget. 
- 2048 bit
- 3072 bit
- 4096 bit

### <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>A megadott számú napon belül lévő tanúsítványok kezelése (előzetes verzió)
A szolgáltatás leállást tapasztalhat, ha egy nem megfelelően figyelt tanúsítvány nem kerül elforgatásra a lejárat előtt. Ez a szabályzat kritikus fontosságú annak biztosításához, hogy a Key vaultban tárolt tanúsítványok figyelése megtörténjen. Azt javasoljuk, hogy a szabályzatot többször alkalmazza különböző lejárati küszöbértékekkel, például 180, 90, 60 és 30 napos küszöbértékekkel. Ez a szabályzat a tanúsítvány lejáratának figyelésére és osztályozására használható a szervezetben. 

## <a name="example-scenario"></a>Példaforgatókönyv

Az 100-es tanúsítványokat tartalmazó több csapat által használt kulcstartót kezelheti, és biztosítania kell, hogy a kulcstartóban lévő egyik tanúsítvány ne legyen két évnél hosszabb ideig érvényes.

1. Rendelje hozzá a [tanúsítvány érvényességi időtartamának kezelése](#manage-certificate-validity-period-preview) házirendet, adja meg, hogy a tanúsítvány maximális érvényességi időtartama 24 hónap, és állítsa be a szabályzat hatását "audit" értékre. 
1. A [megfelelőségi jelentést a Azure Portal](#view-compliance-results)tekintheti meg, és azt is felfedezheti, hogy 20 tanúsítvány nem megfelelő, és > 2 évig érvényes, és a fennmaradó tanúsítványoknak megfelelőek. 
1. Felveszi Önnel a kapcsolatot a tanúsítványok tulajdonosainak, és közli az új biztonsági követelménysel, hogy a tanúsítványok nem lehetnek 2 évnél hosszabb ideig érvényesek. Néhány csapat válaszol, és a tanúsítványok 15-én megújítva a maximális érvényességi időtartam 2 év vagy kevesebb. Más csapatok nem válaszolnak, és továbbra is 5 nem megfelelő tanúsítvány található a kulcstartóban.
1. Megváltoztathatja a "megtagadás" beállításhoz rendelt szabályzat hatásait. Az 5 nem megfelelő tanúsítvány nem vonható vissza, és továbbra is működni fognak. Azonban a két évnél hosszabb érvényességi időtartammal nem újítható meg. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Key Vault szabályzat engedélyezése és kezelése a Azure Portal használatával

### <a name="select-a-policy-definition"></a>Házirend-definíció kiválasztása

1. Jelentkezzen be az Azure Portalra. 
1. Keressen rá a "szabályzat" kifejezésre a keresősáv alatt, és válassza a **házirend**lehetőséget.

    ![A Azure Key Vault működésének áttekintése](./media/policy-img1.png)

1. A házirend ablakban válassza a **definíciók**lehetőséget.

    ![A Azure Key Vault működésének áttekintése](./media/policy-img2.png)

1. A kategória szűrőben törölje az **összes kijelölése** lehetőséget, majd válassza a **Key Vault**lehetőséget. 

    ![A Azure Key Vault működésének áttekintése](./media/policy-img3.png)

1. Most meg kell jelennie a nyilvános előzetes verzióhoz elérhető összes házirendnek, Azure Key Vault. Győződjön meg arról, hogy elolvasta és megértette a szabályzattal kapcsolatos útmutatást, és válassza ki azt a szabályzatot, amelyet hozzá szeretne rendelni egy hatókörhöz.  

    ![A Azure Key Vault működésének áttekintése](./media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Szabályzat társítása hatókörhöz 

1. Válassza ki az alkalmazni kívánt szabályzatot, ebben a példában a **tanúsítvány kezelése érvényességi időszaka** házirend látható. Kattintson a hozzárendelés gombra a bal felső sarokban.

    ![A Azure Key Vault működésének áttekintése](./media/policy-img5.png)
  
1. Válassza ki azt az előfizetést, amelyre alkalmazni szeretné a szabályzatot. Dönthet úgy, hogy az előfizetésen belül csak egy erőforráscsoporthoz korlátozza a hatókört. Ha a szabályzatot a teljes előfizetésre szeretné alkalmazni, és néhány erőforráscsoportot kizár, akkor a kizárási listát is konfigurálhatja. Állítsa be úgy a házirend-kényszerítési választót, hogy **engedélyezve** legyen, ha azt szeretné, hogy a házirend hatása (naplózás vagy megtagadás) **ki legyen kapcsolva** . 

    ![A Azure Key Vault működésének áttekintése](./media/policy-img6.png)

1. Kattintson a képernyő felső részén található parameters (paraméterek) fülre, hogy megadja a maximális érvényességi időtartamot a kívánt hónapokban. A fenti részekben ismertetett útmutatást követve válassza a **naplózás** vagy a **Megtagadás** lehetőséget a szabályzat hatásához. Ezután válassza a felülvizsgálat + létrehozás gombot. 

    ![A Azure Key Vault működésének áttekintése](./media/policy-img7.png)

### <a name="view-compliance-results"></a>Megfelelési eredmények megtekintése

1. Lépjen vissza a szabályzat panelre, és válassza a megfelelőség fület. kattintson arra a szabályzat-hozzárendelésre, amelyben meg szeretné tekinteni a megfelelőségi eredményeket.

    ![A Azure Key Vault működésének áttekintése](./media/policy-img8.png)

1. Ezen a lapon a megfelelő vagy nem megfelelő tárolók alapján szűrheti az eredményeket. Itt láthatja a szabályzat-hozzárendelés hatókörén belül nem megfelelő kulcstartók listáját. A tár nem megfelelőnek minősül, ha a tároló egyik összetevője (tanúsítványa) nem megfelelő. Kiválaszthat egy egyéni tárolót az egyes nem megfelelő összetevők (tanúsítványok) megtekintéséhez. 


    ![A Azure Key Vault működésének áttekintése](./media/policy-img9.png)

1. A nem megfelelő tárolóban található összetevők nevének megtekintése


    ![A Azure Key Vault működésének áttekintése](./media/policy-img10.png)

1. Ha ellenőriznie kell, hogy a felhasználók megtagadják-e az erőforrások létrehozását a Key vaulton belül, kattintson az **összetevő-események (előzetes verzió)** fülre, és tekintse meg a megtagadott tanúsítvány-műveletek összefoglalását a kérelmező és a kérelmek időbélyegével. 


    ![A Azure Key Vault működésének áttekintése](./media/policy-img11.png)

## <a name="feature-limitations"></a>Szolgáltatási korlátozások

A "megtagadás" hatású szabályzatok kiosztása akár 30 percig is eltarthat (átlagos eset) és 1 óra (legrosszabb esetben), hogy megtagadja a nem megfelelő erőforrások létrehozását. Egy tár meglévő összetevőinek kiértékelése akár 1 órát (átlagos esetet) és 2 órát (legrosszabb esetben) is igénybe vehet, mielőtt a portál felhasználói felületén megtekintsék a megfelelőségi eredményeket. Ha a megfelelőségi eredmények "nem elindítottként" jelennek meg, akkor az a következő okok miatt fordulhat elő:
- A szabályzat-értékelés még nem fejeződött be. A kezdeti értékelési késés a legrosszabb esetben akár 2 órát is igénybe vehet. 
- Nincsenek kulcstárolók a szabályzat-hozzárendelés hatókörében.
- Nincsenek kulcstárolók a szabályzat-hozzárendelés hatókörében található tanúsítványokkal. 

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Policy szolgáltatásról](../governance/policy/overview.md)
- Tekintse meg a következő mintát: [Key Vault virtuális hálózati végpont nélküli](../governance/policy/samples/keyvault-no-vnet-rules.md) tárolók

