---
title: Az Azure Key Vault integrálása az Azure-szabályzattal
description: Ismerje meg, hogyan integrálható az Azure Key Vault az Azure-szabályzattal
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/28/2020
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.openlocfilehash: 6b54dc27f8a3e88dedb0552b1ac7fb675d75121a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424593"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Az Azure Key Vault integrálása az Azure-szabályzattal

[Az Azure Policy](../../governance/policy/index.yml) egy cégirányítási eszköz, amely lehetővé teszi a felhasználók számára, hogy az Azure-környezetük nagy méretekben naplózása és kezelése. Az Azure Policy lehetővé teszi, hogy az Azure-erőforrásokon korlátokat helyezzen el annak érdekében, hogy azok megfeleljenek a hozzárendelt szabályzati szabályoknak. Lehetővé teszi a felhasználók számára, hogy naplózást, valós idejű kényszerítést és az Azure-környezet ük szervizelését hajtsanak végre. A szabályzat által végrehajtott auditok eredményei elérhetők lesznek a felhasználók számára egy megfelelőségi irányítópulton, ahol láthatják, hogy mely erőforrások és összetevők felelnek meg, és melyek nem.  További információt az [Azure Policy szolgáltatás áttekintése című témakörben](../../governance/policy/overview.md)talál.

Példa használati esetekre:

- Szeretné javítani a vállalat biztonsági állapotát azáltal, hogy a vállalat kulcstárolóiban a minimális kulcsméretekre és a tanúsítványok maximális érvényességi időtartamára vonatkozó követelményeket valósít meg, de nem tudja, hogy mely csapatok lesznek megfelelőek, és melyek nem. 
- Jelenleg nincs megoldás a szervezeten belüli naplózás végrehajtására, vagy manuális auditálást végez a környezetről azáltal, hogy a szervezeten belül kéri a megfelelő csoportokat. Keresi a módját, hogy automatizálja ezt a feladatot, végezze el a naplózást valós időben, és garantálja a naplózás pontosságát.
- Szeretné érvényesíteni a vállalati biztonsági házirendeket, és le állítani a személyeket az önaláírt tanúsítványok létrehozásában, de nincs automatikus módja a létrehozásuk letiltásának. 
- Szeretné pihenni a tesztcsapatok bizonyos követelményeit, de szeretné, hogy az éles környezet szigorú szabályozása. Szüksége van egy egyszerű, automatizált módon külön kényszerítése a források. 
- Biztos szeretne lenni abban, hogy élő webhely-probléma esetén visszaállíthatja az új házirendek végrehajtását. A házirend kényszerítésének kikapcsolásához egykattintásos megoldásra van szükség. 
- Egy harmadik féltől származó megoldásra támaszkodik a környezet naplózásához, és belső Microsoft-ajánlatot szeretne használni. 

## <a name="types-of-policy-effects-and-guidance"></a>A szakpolitikai hatások és iránymutatások típusai

**Naplózás:** Ha egy házirend hatása naplózásra van állítva, a házirend nem okoz törésmódosításokat a környezetben. Csak az okat a házirend-megfelelőségi irányítópulton nem megfelelőnek jelölő összetevőket, például azokat a tanúsítványokat, amelyek nem felelnek meg a házirend-definícióknak, hogy nem megfelelőként jelöli meg ezeket az összetevőket. A naplózás az alapértelmezett, ha nincs bejelölve házirend-hatás. 

**Megtagadás**: Ha egy házirend hatása megtagadásra van állítva, a házirend blokkolja az új összetevők, például a tanúsítványok létrehozását, valamint a meglévő összetevők új verzióinak blokkolását, amelyek nem felelnek meg a házirend-definíciónak. A kulcstartón belül meglévő nem megfelelő erőforrásokat ez nem érinti. Az "audit" képességek továbbra is működnek.

## <a name="available-built-in-policy-definitions"></a>Elérhető "Beépített" házirend-definíciók

A Key Vault létrehozott egy szabályzatkészletet, amelyet a tanúsítványok kezeléséhez közös forgatókönyvekhez rendelhet hozzá. Ezek a szabályzatok "beépített", ami azt jelenti, hogy nem kell írni a különböző JSON-hoz engedélyezésük, és elérhetők az Azure Portalon az Ön számára. Bizonyos paramétereket továbbra is testreszabhat, hogy azok megfeleljenek a szervezet igényeinek. 

A nyolc előzetes verziójú szabályzat a következők.

### <a name="manage-certificate-validity-period-preview"></a>Tanúsítvány érvényességi időtartamának kezelése (előzetes verzió)

Ez a házirend lehetővé teszi a key vaultban tárolt tanúsítványok maximális érvényességi idejének kezelését. A tanúsítványok maximális érvényességi idejének korlátozása jó biztonsági gyakorlat. Ha a tanúsítvány személyes kulcsa észlelés nélkül sérül, a rövid életű tanúsítványok használata minimálisra csökkenti a folyamatos károk időkeretét, és csökkenti a tanúsítvány értékét a támadószámára. 

### <a name="manage-allowed-certificate-key-types-preview"></a>Az engedélyezett tanúsítványkulcs-típusok kezelése (előzetes verzió)
Ez a házirend lehetővé teszi, hogy korlátozza a tanúsítvány típusát, amely a key vaultban lehet. Ezzel a házirenddel meggyőződhet arról, hogy a tanúsítvány személyes kulcsai RSA, ECC vagy HSM-támogatottak. Az alábbi listából választhatja ki, hogy mely tanúsítványtípusok engedélyezettek.
- RSA
- RSA - HSM
- Ecc 
- ECC - HSM 

### <a name="manage-certificate-lifetime-action-triggers-preview"></a>Tanúsítvány élettartama műveleteseményindítók kezelése (előzetes verzió)

Ez a házirend lehetővé teszi, hogy kezelje az élettartamra szóló műveletet a tanúsítványok, amelyek vagy egy bizonyos számú napon belül a lejárati, vagy elérte a felhasználható élettartamuk egy bizonyos százalékát. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Integrált hitelesítésszolgáltató által kiállított tanúsítványok kezelése (előzetes verzió)

Ha egy Key Vault integrált hitelesítésszolgáltatót (Digicert vagy GlobalSign) használ, és azt szeretné, hogy a felhasználók az egyik vagy valamelyik szolgáltatót használják, ezzel a házirenddel naplózhatja vagy kényszerítheti a kijelölést. Ez a házirend is használható naplózására vagy megtagadni az önaláírt tanúsítványok létrehozása a key vaultban. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Integrált hitelesítésszolgáltató által kiállított tanúsítványok kezelése (előzetes verzió)

Ha olyan belső hitelesítésszolgáltatót vagy hitelesítésszolgáltatót használ, amely nincs integrálva a kulcstartóval, és azt szeretné, hogy a felhasználók egy megadott listából származó hitelesítésszolgáltatót használjanak, ezzel a házirenddel kiállítónév szerint létrehozhategy engedélyezett hitelesítésszolgáltatók listáját. Ez a házirend is használható naplózására vagy megtagadni az önaláírt tanúsítványok létrehozása a key vaultban. 

### <a name="manage-allowed-curve-names-for-elliptic-curve-cryptography-certificates-preview"></a>Az elliptikus görbe titkosítási tanúsítványainak engedélyezett ívnevének kezelése (előzetes verzió)
Ha elliptikus görbe-titkosítást vagy ECC-tanúsítványokat használ, az alábbi listából testreszabhatja a görbenevek engedélyezett listáját. Az alapértelmezett beállítás lehetővé teszi az összes következő ívnevet. 
- P-256
- P-256K
- P-384
- P-521

### <a name="manage-minimum-key-size-for-rsa-certificates-preview"></a>Az RSA-tanúsítványok minimális kulcsméretének kezelése (előzetes verzió)
Ha RSA-tanúsítványokat használ, kiválaszthatja azt a minimális kulcsméretet, amelynek a tanúsítványoknak rendelkeznie kell. Az alábbi listából választhat egy lehetőséget. 
- 2048 bites
- 3072 bites
- 4096 bites

### <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>A megadott számú elévülési napon belüli tanúsítványok kezelése (előzetes verzió)
A szolgáltatás kimaradást tapasztalhat, ha egy tanúsítvány, amely nem megfelelően figyelt van elforgatva lejárta előtt. Ez a házirend elengedhetetlen annak biztosításához, hogy a key vaultban tárolt tanúsítványok figyelt. Javasoljuk, hogy ezt a szabályzatot többször alkalmazza különböző lejárati küszöbértékekkel, például 180, 90, 60 és 30 napos küszöbértékekkel. Ez a házirend a szervezetben a tanúsítványok lejáratának figyelésére és osztályozására használható. 

## <a name="example-scenario"></a>Példaforgatókönyv

A 100 tanúsítványt tartalmazó több csapat által használt kulcstartót kezeli, és győződjön meg arról, hogy a key vault egyik tanúsítványa sem érvényes 2 évnél hosszabb ideig.

1. A [Tanúsítvány érvényességi időtartamának kezelése](#manage-certificate-validity-period-preview) házirendet, megadhatja, hogy a tanúsítvány maximális érvényességi ideje 24 hónap, és a házirend hatását "naplózásra" állítja. 
1. A [megfelelőségi jelentést az Azure Portalon](#view-compliance-results)tekintheti meg, és felderítheti, hogy 20 tanúsítvány nem megfelelő és > 2 évig érvényes, a fennmaradó tanúsítványok pedig megfelelőek. 
1. Kapcsolatba lép a tanúsítványok tulajdonosaival, és közli az új biztonsági követelményt, hogy a tanúsítványok 2 évnél hosszabb ideig nem érvényesek. Egyes csapatok válaszolnak, és 15 tanúsítványt újítottak meg legfeljebb 2 éves vagy annál rövidebb érvényességi idővel. Más csapatok nem válaszolnak, és továbbra is 5 nem megfelelő tanúsítvány található a kulcstartóban.
1. Módosítja a "megtagadás" házirendhez rendelt házirend hatását. Az 5 nem megfelelő tanúsítvány nem vonható vissza, és továbbra is működnek. Ezeket azonban nem lehet megújítani 2 évnél hosszabb érvényességi idővel. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Key Vault-szabályzat engedélyezése és kezelése az Azure Portalon keresztül

### <a name="select-a-policy-definition"></a>Házirend-definíció kiválasztása

1. Jelentkezzen be az Azure Portalra. 
1. Keressen rá a "Házirend" kifejezésre a keresősávban, és válassza ki a **házirendet.**

    ![Az Azure Key Vault működésének áttekintése](../media/policy-img1.png)

1. A Házirend ablakban válassza a **Definíciók**lehetőséget.

    ![Az Azure Key Vault működésének áttekintése](../media/policy-img2.png)

1. A Kategóriaszűrőben törölje az **Összes kijelölése** jelölőnégyzet jelölését, és válassza a **Key Vault**elemet . 

    ![Az Azure Key Vault működésének áttekintése](../media/policy-img3.png)

1. Most már látnia kell az összes elérhető szabályzatot a Nyilvános előzetes verzió, az Azure Key Vault számára. Győződjön meg arról, hogy elolvasta és megértette a fenti házirend-útmutató szakaszt, és válassza ki a hatókörhöz rendelni kívánt házirendet.  

    ![Az Azure Key Vault működésének áttekintése](../media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Házirend hozzárendelése hatókörhöz 

1. Válassza ki az alkalmazni kívánt házirendet ebben a példában a **Tanúsítvány érvényességi időszakának kezelése** házirend jelenik meg. Kattintson a hozzárendelés gombra a bal felső sarokban.

    ![Az Azure Key Vault működésének áttekintése](../media/policy-img5.png)
  
1. Válassza ki azt az előfizetést, amelyre alkalmazni szeretné a szabályzatot. Dönthet úgy, hogy korlátozza a hatókört, hogy csak egy erőforráscsoport egy előfizetésen belül. Ha a szabályzatot a teljes előfizetésre szeretné alkalmazni, és ki szeretne zárni néhány erőforráscsoportot, beállíthat egy kizárási listát is. Állítsa a házirend-kényszerítésválasztót **Engedélyezve értékre,** ha azt szeretné, hogy a házirend (naplózás vagy letiltás) hatása bekövetkezzen, vagy **tiltsa le** a hatást (naplózás vagy megtagadás). 

    ![Az Azure Key Vault működésének áttekintése](../media/policy-img6.png)

1. Kattintson a paraméterek fülre a képernyő tetején, hogy megadja a maximális érvényességi időtartamot hónapokban. Válassza ki a **naplózás** vagy **a megtagadás** a szabályzat hatása a fenti szakaszokban található útmutatást követve. Ezután válassza a felülvizsgálat + create gombot. 

    ![Az Azure Key Vault működésének áttekintése](../media/policy-img7.png)

### <a name="view-compliance-results"></a>Megfelelőségi eredmények megtekintése

1. Lépjen vissza a Szabályzat panelre, és válassza ki a megfelelőségi lapot.

    ![Az Azure Key Vault működésének áttekintése](../media/policy-img8.png)

1. Ezen a lapon szűrheti az eredményeket megfelelő vagy nem megfelelő tárolók szerint. Itt láthatja a nem megfelelő kulcstartók listáját a házirend-hozzárendelés hatókörén belül. A tároló nem megfelelőnek minősül, ha a tárolóban lévő összetevők (tanúsítványok) bármelyike nem megfelelő. Az egyes tárolók kiválasztásával megtekintheti az egyes nem megfelelő összetevőket (tanúsítványokat). 


    ![Az Azure Key Vault működésének áttekintése](../media/policy-img9.png)

1. A tárolón belüli nem megfelelő összetevők nevének megtekintése


    ![Az Azure Key Vault működésének áttekintése](../media/policy-img10.png)

1. Ha ellenőriznie kell, hogy a felhasználók megtagadják-e az erőforrások létrehozásának lehetőségét a kulcstárolón belül, a **Komponensesemények (előzetes verzió)** fülre kattintva megtekintheti a megtagadott tanúsítványműveletek összegzését a kérelmezővel és a kérelmek időbélyegeit. 


    ![Az Azure Key Vault működésének áttekintése](../media/policy-img11.png)

## <a name="feature-limitations"></a>Szolgáltatás korlátai

A "megtagadás" hatású házirend hozzárendelése akár 30 perces (átlagos eset) és 1 órát is igénybe vehet (legrosszabb esetben) a nem megfelelő erőforrások létrehozásának megtagadása. A meglévő összetevők házirend-kiértékelése a tárolóban is eltarthat akár 1 óra (átlagos eset) és 2 óra (legrosszabb esetben), mielőtt megfelelőségi eredmények láthatók a portál felhasználói felületén. Ha a megfelelőségi eredmények "Nem kezdődtek el" néven jelennek meg, annak oka a következő oka lehet:
- A kötvényértékelés még nem fejeződött be. A kezdeti kiértékelési késés a legrosszabb esetben akár 2 órát is igénybe vehet. 
- Nincsenek kulcstartók a házirend-hozzárendelés hatókörében.
- Nincsenek tanúsítványokat tároló kulcstartók a házirend-hozzárendelés hatókörén belül. 

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure Policy szolgáltatásról](../../governance/policy/overview.md)
- Lásd: Key Vault-minták: [A Key Vault beépített házirend-definíciói](../../governance/policy/samples/built-in-policies.md#key-vault)