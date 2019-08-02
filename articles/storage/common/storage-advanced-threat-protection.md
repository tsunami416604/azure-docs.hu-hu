---
title: Advanced Threat Protection for Azure Storage
description: Az Azure Storage komplex veszélyforrások elleni védelem konfigurálása a fiókban észlelt rendellenességek észlelésére és a fiókhoz való hozzáférésre vonatkozó lehetséges ártalmas kísérletek bejelentésére.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: bb2d5733704b0b31dc010cec2a90e99e1be07b56
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592031"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Advanced Threat Protection for Azure Storage

Az Azure Storage-hoz nyújtott komplex veszélyforrások elleni védelem egy további biztonságiintelligencia-réteget ad, amely észleli a tárfiókok elérésére és felhasználására tett szokatlan és feltehetően ártalmas kísérleteket. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelését anélkül, hogy biztonsági szakértőnek kellene lennie, vagy biztonsági figyelő rendszereket kellene kezelnie. 

A biztonsági riasztások akkor lépnek életbe, ha a tevékenységben anomáliák vannak.  Ezek a biztonsági riasztások integrálva vannak [Azure Security Centerekkel](https://azure.microsoft.com/services/security-center/), és e-mailben is elküldjük az előfizetés-rendszergazdáknak, a gyanús tevékenységek részleteivel és a fenyegetések kivizsgálásával és javításával kapcsolatos ajánlásokkal együtt.

> [!NOTE]
> * Az Azure Storage komplex veszélyforrások elleni védelme jelenleg csak a blob Storage esetében érhető el.
> * A díjszabással kapcsolatos részletekért, beleértve az ingyenes 30 napos próbaverziót is, tekintse meg a [Azure Security Center díjszabási oldalát]( https://azure.microsoft.com/pricing/details/security-center/).
> * Az Azure Storage-hoz készült ATP szolgáltatás jelenleg nem érhető el az Azure governmentben és a szuverén Felhőbeli régiókban.

Az Azure Storage komplex veszélyforrások elleni védelme a fenyegetések észlelésére szolgáló olvasási, írási és törlési kérések diagnosztikai naplóit is betölti. A komplex veszélyforrások elleni védelemből származó riasztások vizsgálatához a kapcsolódó tárolási tevékenységeket Storage Analytics naplózás használatával tekintheti meg. További információ: [Storage Analytics naplózásának konfigurálása](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Komplex veszélyforrások elleni védelem beállítása 

### <a name="using-the-portal"></a>A portál használata

1. Indítsa el a Azure Portal [https://portal.azure.com](https://portal.azure.com/)a következő helyen:.

2. Navigáljon a védelemmel ellátni kívánt Azure Storage-fiók konfigurációs lapjára. A **Beállítások** lapon válassza a komplex **veszélyforrások elleni védelem**lehetőséget.

3. A **speciális veszélyforrások elleni védelem** konfiguráció paneljén
    * **Az** összetett *veszélyforrások elleni védelem* bekapcsolása
    * Kattintson a **Save (Mentés** ) gombra az új vagy frissített összetett veszélyforrások elleni védelmi szabályzat mentéséhez. (A rendszerkép árai csak példaként szolgálnak.)

![Az Azure Storage komplex veszélyforrások elleni védelem bekapcsolása](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>Az Azure Security Center használatával

Amikor előfizet a Azure Security Center Standard csomagra, a komplex veszélyforrások elleni védelem automatikusan be lesz állítva az összes Storage-fiókra. A következő módon engedélyezheti vagy letilthatja az összetett veszélyforrások elleni védelmet a Storage-fiókjaihoz egy adott előfizetésben:

1. A [Azure Portal](https://portal.azure.com) **Azure Security Center** elindítása.
1. A főmenüben kattintson a **díjszabás & beállítások**elemre.
1. Kattintson arra az előfizetésre, amelyre engedélyezni vagy le szeretné tiltani a veszélyforrások elleni védelmet a Storage-fiókjaihoz.

    ![Előfizetés kiválasztása](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Kattintsona díjszabási csomag elemre.
1. Az **árképzési rétegek kiválasztása erőforrástípus szerint** szakaszban, a Storage- **fiókok** sorban kattintson az **engedélyezve** vagy aLetiltva lehetőségre.

    ![ATP engedélyezése Security Center](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Kattintson a **Save** (Mentés) gombra.

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager-sablonok használata

Egy Azure Resource Manager sablon használatával üzembe helyezhet egy Azure Storage-fiókot, amelyen engedélyezve van az összetett veszélyforrások védelme. További információ: Storage- [fiók komplex veszélyforrások elleni védelemmel](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="using-azure-policy"></a>Azure Policy használata

Egy Azure Policy használatával engedélyezheti a komplex veszélyforrások elleni védelmet egy adott előfizetés vagy erőforráscsoport alatt lévő Storage-fiókok között.

1. Indítsa el az Azure **Policy-fogalommeghatározások** lapot.

1. Keresse meg a komplex **veszélyforrások elleni védelem telepítése a Storage** -fiókokra vonatkozó házirendet.

     ![Keresési szabályzat](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. Válasszon ki egy Azure-előfizetést vagy erőforráscsoportot.

    ![Előfizetés vagy csoport kiválasztása](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Rendelje hozzá a szabályzatot.

    ![Házirend-definíciók lap](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>A REST API használata
A REST API-parancsok használatával létrehozhat, frissíthet vagy beszerezhet egy adott Storage-fiók komplex veszélyforrások elleni védelem beállítását.

* [Komplex veszélyforrások elleni védelem – létrehozás](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Komplex veszélyforrások elleni védelem – Get](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

Használja a következő PowerShell-parancsmagokat:

  * [Komplex veszélyforrások elleni védelem engedélyezése](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [Komplex veszélyforrások elleni védelem](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [A komplex veszélyforrások elleni védelem letiltása](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>Biztonsági rendellenességek megismerése

Ha a tárolási tevékenységek rendellenességeket tapasztalnak, e-mailben értesítést kap a gyanús biztonsági eseményről. Az esemény részletei a következők:

* A rendellenesség természete
* A Storage-fiók neve
* Az esemény időpontja
* A tárolási típus
* A lehetséges okok 
* A vizsgálat lépései
* A szervizelés lépései


Az e-mail emellett tartalmazza a lehetséges okokat és az ajánlott műveleteket is a lehetséges fenyegetések kivizsgálásához és enyhítéséhez.

![Azure Storage komplex veszélyforrások elleni védelem – riasztási e-mail](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Az aktuális biztonsági riasztásokat a Azure Security Center [biztonsági riasztások csempéről](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts)tekintheti meg és kezelheti. Ha egy adott riasztásra kattint, a rendszer részletesen ismerteti az aktuális fenyegetés kivizsgálásával és a jövőbeli fenyegetésekkel kapcsolatos műveleteket.

![Azure Storage komplex veszélyforrások elleni védelem – riasztási e-mail](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Védelmi riasztások

A riasztásokat szokatlan és potenciálisan ártalmas kísérletek generálják a Storage-fiókok eléréséhez vagy kiaknázásához. Ezek az események a következő riasztásokat indíthatják el:

### <a name="anomalous-access-pattern-alerts"></a>Rendellenes hozzáférési minta riasztásai

* **Hozzáférés szokatlan helyről**: Ez a riasztás akkor aktiválódik, ha valaki egy szokatlan földrajzi helyről fér hozzá egy Storage-fiókhoz.
Lehetséges okok:
   * Egy támadó hozzáfért a Storage-fiókhoz
   * Egy megbízható felhasználó új helyről fér hozzá a Storage-fiókhoz
 
* **Alkalmazás anomália**: Ez a riasztás azt jelzi, hogy egy szokatlan alkalmazás hozzáfért ehhez a Storage-fiókhoz. Lehetséges okok:
   * Egy támadó új alkalmazás használatával fér hozzá a Storage-fiókhoz.
   * Egy megbízható felhasználó új alkalmazást vagy böngészőt használt a Storage-fiók eléréséhez.

* **Névtelen hozzáférés**: Ez a riasztás azt jelzi, hogy ez a fiók névtelenül (azaz hitelesítés nélkül) lett elküldve, ami nem várt, mint a fiók legutóbbi hozzáférési mintája.
Lehetséges okok:
   * Egy támadó nyilvános olvasási hozzáférést kapott egy tárolóhoz.
   * Egy megbízható felhasználó vagy alkalmazás nyilvános olvasási hozzáférést használt egy tárolóhoz.

* **Tor**-anomália: Ez a riasztás azt jelzi, hogy ez a fiók sikeresen elérhető egy olyan IP-címről, amely a Tor (egy anonimizálásával proxy) aktív kilépési csomópontjának ismert. Ennek a riasztásnak a súlyossága figyelembe veszi a használt hitelesítési típust (ha van ilyen), és hogy ez az ilyen hozzáférés első esete-e.
Lehetséges okok:
   * A támadó a Tor használatával fér hozzá a Storage-fiókhoz.
   * Egy megbízható felhasználó a Tor használatával fér hozzá a Storage-fiókhoz.


### <a name="anomalous-extractupload-alerts"></a>Rendellenes kinyerési/feltöltési riasztások

* **Az adatkiszűrése**: Ez a riasztás azt jelzi, hogy a tárolón a legutóbbi tevékenységhez képest szokatlanul nagy mennyiségű adattal lett kibontva. Lehetséges okok:
   * Egy támadó nagy mennyiségű adattal kivont egy tárolóból. (Például: adatok kiszűrése/megszegése, jogosulatlan adatátvitel)
   * Egy megbízható felhasználó vagy alkalmazás szokatlan mennyiségű adattal kibontotta a tárolót. (Például: karbantartási tevékenység)

* **Váratlan törlés**: Ez a riasztás azt jelzi, hogy egy vagy több váratlan törlési művelet történt egy Storage-fiókban, a fiók legutóbbi tevékenységéhez képest. Lehetséges okok:
   * Egy támadó törölte az adatait a Storage-fiókjából.
   * Egy megbízható felhasználó szokatlan törlést hajtott végre.

* **Azure Cloud Service-csomag feltöltése**: Ez a riasztás azt jelzi, hogy egy Azure Cloud Service-csomag (. cspkg fájl) szokatlan módon lett feltöltve egy Storage-fiókba, a fiókhoz tartozó közelmúltbeli tevékenységhez képest. Lehetséges okok: 
   * Egy támadó arra készül, hogy rosszindulatú kódot helyezzen üzembe a Storage-fiókból egy Azure Cloud Service-be.
   * Egy megbízható felhasználó előkészítette a megbízható szolgáltatás központi telepítését.

### <a name="suspicious-storage-activities-alerts"></a>Gyanús tárolási tevékenységek riasztásai

* **Hozzáférési engedély módosítása**: Ez a riasztás azt jelzi, hogy a tárolási tároló hozzáférési engedélyei szokatlan módon módosultak. Lehetséges okok: 
   * A támadó megváltoztatta a tároló engedélyeit, hogy gyengítse a biztonságot.
   * Egy megbízható felhasználó módosította a tároló engedélyeit.

* **Hozzáférés ellenőrzése**: Ez a riasztás azt jelzi, hogy a Storage-fiók hozzáférési engedélyei szokatlan módon lettek megvizsgálva, a fiók legutóbbi tevékenységéhez képest. Lehetséges okok: 
   * Egy támadó egy jövőbeli támadás felderítését végezte el.
   * Egy megbízható felhasználó karbantartási műveleteket hajtott végre a Storage-fiókon.

* **Adatelemzés**: Ez a riasztás azt jelzi, hogy a Storage-fiókban lévő Blobok vagy tárolók szokatlan módon vannak felsorolva a fiók legutóbbi tevékenységéhez képest. Lehetséges okok: 
   * Egy támadó egy jövőbeli támadás felderítését végezte el.
   * Egy megbízható felhasználó vagy alkalmazás logikája a Storage-fiókban található meg.






## <a name="next-steps"></a>További lépések

* További információ az [Azure Storage-fiókok naplóiról](/rest/api/storageservices/About-Storage-Analytics-Logging)

* További információ a [Azure Security Center](../../security-center/security-center-intro.md)
