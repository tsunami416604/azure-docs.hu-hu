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
ms.openlocfilehash: 2a88e778458da3c5faace401863998dda746ac75
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051492"
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

Az aktuális biztonsági riasztásokat a Azure Security Center [biztonsági riasztások csempéről](../../security-center/security-center-managing-and-responding-alerts.md)tekintheti meg és kezelheti. Ha egy adott riasztásra kattint, a rendszer részletesen ismerteti az aktuális fenyegetés kivizsgálásával és a jövőbeli fenyegetésekkel kapcsolatos műveleteket.

![Azure Storage komplex veszélyforrások elleni védelem – riasztási e-mail](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Védelmi riasztások

A riasztásokat szokatlan és potenciálisan ártalmas kísérletek generálják a Storage-fiókok eléréséhez vagy kiaknázásához. A riasztások listájáért lásd: [Azure Storage](../../security-center/security-center-alerts-data-services.md#azure-storage) -riasztások

## <a name="next-steps"></a>További lépések

* További információ az [Azure Storage-fiókok naplóiról](/rest/api/storageservices/About-Storage-Analytics-Logging)

* További információ a [Azure Security Center](../../security-center/security-center-intro.md)
