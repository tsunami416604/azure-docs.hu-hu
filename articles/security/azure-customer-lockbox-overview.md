---
title: Ügyfél kulcstároló, Microsoft Azure
description: A Microsoft Azure, amely a felhőbeli szolgáltató hozzáférés fölötti felügyelet biztosít, amikor a Microsoft hozzáférhet az ügyféladatokhoz előfordulhat, hogy kell ügyfél kulcstároló technikai áttekintése.
author: cabailey
ms.service: security
ms.topic: article
ms.author: cabailey
manager: barbkess
ms.date: 06/20/2019
ms.openlocfilehash: 0ee2dde5a941d069f5b745eafb35df780f657a47
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312620"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Ügyfél kulcstároló, Microsoft Azure

> [!NOTE]
> Ez a funkció használatához a szervezetnek rendelkeznie kell egy [Azure-támogatási csomag](https://azure.microsoft.com/support/plans/) minimális szintű **fejlesztői**.

Ügyfél kulcstároló, Microsoft Azure-ügyfelek számára, hogy tekintse át és hagyja jóvá vagy felhasználói hozzáférési kérelmek elutasítása felületet biztosít. Azokban az esetekben, ahol egy Microsoft-mérnök el kell érnie vásárlói adatokat egy támogatási kérést során használható.

Ez a cikk ismerteti, hogyan ügyfél kulcstároló kérelmek vannak kezdeményezett, nyomon követi, és tárolja az újabb értékelések és eseményeket.

Ügyfél kulcstároló már általánosan elérhető, és jelenleg engedélyezve van a távoli asztali hozzáférés a virtuális gépek számára.

## <a name="workflow"></a>Munkafolyamat

Egy ügyfél kulcstároló kérelem általános munkafolyamat lépései.

1. Valaki egy szervezet rendelkezik az Azure-beli számítási problémáját.

2. Miután ezt a személyt hibaelhárítást végez a probléma, de nem javítható, egy támogatási jegyet az megnyitja a [az Azure portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). A jegy hozzá van rendelve egy Azure-ügyfelek támogatási szakértőhöz.

3. Egy Azure-támogatási szakértő áttekinti a szolgáltatáskérést, és határozza meg a következő lépéseket a probléma megoldása érdekében.

4. Ha a támogatási szakértőhöz nem lehet a probléma elhárításához standard eszközöket és a telemetriai adatok segítségével a következő lépés az emelt szintű engedélyek kéréséhez engedélyezték (JIT) hozzáférési szolgáltatás használatával. Ezt a kérelmet az eredeti támogatási szakértő közötti lehet. Vagy lehet egy másik mérnök, mert a probléma eszkalálása az Azure fejlesztési és üzemeltetési csapatának.

5. A hozzáférés után az Azure-mérnök, Just-In-Time kérelem elküldését szolgáltatás kiértékeli a kérést, figyelembe véve tényezőket például:
    - A hatókör az erőforrás
    - A kérelmező-e egy elkülönített identitás vagy a multi-factor authentication szolgáltatás használatával
    - Engedélyszintekben
    
    Az igény szerinti szabály alapján, a kérelem is magukban foglalhatnak egy belső Microsoft a jóváhagyók a jóváhagyási. A jóváhagyó lehet például az ügyfél-támogatási érdeklődő vagy a DevOps segítségével.

6. A kérelem a vásárlói adatokhoz közvetlen hozzáférésre van szüksége, amikor egy ügyfél kulcstároló kérést kezdeményeznek. Ha például távoli asztali hozzáférés ügyfél virtuális géphez.
    
    A kérelem már az egy **ügyfél értesítést kap** állapotban, mielőtt hozzáférést adna az ügyfél jóváhagyásra vár.

7. Az ügyfél szervezetben, a felhasználó, aki rendelkezik a [tulajdonosi szerepkör](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) az Azure-előfizetés kap egy e-mailt Microsoft, a függőben lévő hozzáférési kérelemmel kapcsolatban értesíteni őket. A vásárlói kulcstároló kéréseket Ez a személy már a kijelölt jóváhagyónak.
    
    Például szolgáló e-mail:
    
    ![Az Azure vásárlói kulcstároló - e-mail-értesítés](./media/azure-customer-lockbox/customer-lockbox-email-notification.png)

8. Az e-mail-értesítés mutató hivatkozást tartalmaz a **ügyfél kulcstároló** panel az Azure Portalon. Használja ezt a hivatkozást, a kijelölt jóváhagyó jelentkezik be az Azure Portalon minden függőben lévő kéréseket, amelyek a cégük rendelkezik az ügyfél kulcstároló megtekintése:
    
    ![Az Azure vásárlói kulcstároló - kezdőlapja](./media/azure-customer-lockbox/customer-lockbox-landing-page.png)
    
   A kérelem az ügyfél üzenetsorban napig maradnak, négy. Ezt követően a hozzáférési kérelem automatikusan lejárnak, és nincs férjenek hozzá a Microsoft szakemberei.

9. A függőben lévő kérés részletei lekéréséhez a kijelölt jóváhagyó választhat a kulcstároló kérés **függőben lévő kérelmek**:
    
    ![Az Azure vásárlói kulcstároló - megtekintheti a függőben lévő kérelem](./media/azure-customer-lockbox/customer-lockbox-pending-requests.png)

10. A kijelölt jóváhagyó is választhat a **SZOLGÁLTATÁSKÉRÉS azonosítója** megtekintése a támogatási jegy kérelem, az eredeti felhasználó által létrehozott. Ezt az információt biztosít, miért Support bevonhat környezetét, és a jelentett probléma előzményeit. Példa:
    
    ![Az Azure vásárlói kulcstároló - megtekintése a támogatási jegy kérelem](./media/azure-customer-lockbox/customer-lockbox-support-ticket.png)

11. A kijelölt jóváhagyó kiválasztása után tekintse át a kérést, **jóváhagyás** vagy **Megtagadás**:
    
    ![Az Azure vásárlói kulcstároló - válassza ki, jóváhagyás vagy Megtagadás](./media/azure-customer-lockbox/customer-lockbox-approval.png)
    
    Eredményeként a kijelölés:
    - **Hagyja jóvá**:  A Microsoft-mérnök hozzáférhessenek a mobileszközeiken. A hozzáférés alapértelmezett időtartamra nyolc óra.
    - **Megtagadási**: A Microsoft-mérnök emelt szintű hozzáférés kérelmének elutasítása esetén, és nem további műveletet.

A naplózási célokra, a munkafolyamat végrehajtott műveletek bejelentkezett [ügyfél kulcstároló kérelmekről készült naplók](#auditing-logs).

## <a name="auditing-logs"></a>Naplói

Ügyfél kulcstároló naplóit tevékenységeket tartalmazó naplók vannak tárolva. Az Azure Portalon válassza ki a **tevékenységeket tartalmazó naplók** ügyfél kulcstároló kérelmek kapcsolódó naplózási információk megtekintéséhez. Szűrhet az adott műveletek, például:
- **Kulcstároló-kérelem elutasítása**
- **Kulcstároló-kérelem létrehozása**
- **Kulcstároló-kérelem jóváhagyása**
- **Kulcstároló-kérelem lejárati**

Példa:

![Az Azure vásárlói kulcstároló - Tevékenységnaplók](./media/azure-customer-lockbox/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios"></a>Támogatott szolgáltatások és alkalmazási helyzetek

A következő szolgáltatások és alkalmazási helyzetek jelenleg nyilvánosan ügyfél kulcstároló rendelkezésre állása.

### <a name="remote-desktop-access-to-virtual-machines"></a>A virtuális gépek távoli asztali hozzáférés

Ügyfél kulcstároló jelenleg engedélyezve van a távoli asztali hozzáférés a virtuális gépekhez érkező kérések. A következő számítási feladatokkal támogatottak:
- Platformszolgáltatás (PaaS) – az Azure Cloud Services (webes és feldolgozói szerepkör)
- Szolgáltatott infrastruktúra (IaaS) – Windows és Linux (csak az Azure Resource Manager)
- Virtuális gép méretezési – Windows és Linux

> [!NOTE]
> Klasszikus IaaS-példányok nem támogatottak az ügyfél kulcstároló. Ha a klasszikus IaaS-példányokon futó számítási feladatok, javasoljuk a Kapcsolatcsoportok módosítása klasszikusról Resource Manager üzembe helyezési modellek migrálása. Útmutatásért lásd: [Platform által támogatott áttelepítés IaaS-erőforrások klasszikusból Azure Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Részletes naplók

Távoli asztali hozzáférés érintő forgatókönyvek esetén a Windows-eseménynaplók segítségével tekintse át a Microsoft-mérnök által végrehajtott műveletek. Fontolja meg az események gyűjtését és elemzés céljából a munkaterület az adatok másolása az Azure Security Center használatát. További információkért lásd: [az adatgyűjtést az Azure Security Centerben](../security-center/security-center-enable-data-collection.md).

## <a name="exclusions"></a>Korlátozások

Ügyfél kulcstároló kérelmek nem aktiválódik, a következő műszaki támogatási helyzetekben:

- Egy Microsoft-mérnök szüksége van egy tevékenységgel, amely a szabványos működési eljárásainak kívül esik. Ha például helyre vagy állítsa vissza a szolgáltatás váratlan vagy kiszámíthatatlan forgatókönyvekben.

- Egy Microsoft-mérnök fér hozzá az Azure platform hibaelhárítási részeként, és véletlenül rendelkezik hozzáféréssel az ügyféladatokhoz. Például az Azure hálózati csapata hajt végre, hibaelhárítás, amely a hálózati eszköz csomagrögzítés eredményez. Azonban ha az ügyfél az adatok titkosítva az átvitel közben, a visszafejtés nem tudja olvasni az adatokat.

## <a name="next-steps"></a>További lépések

Ügyfél kulcstároló automatikusan érhető el az összes olyan felhasználóknak, akik már egy [Azure-támogatási csomag](https://azure.microsoft.com/support/plans/) minimális szintű **fejlesztői**.

Amikor egy jogosult támogatási csomaggal rendelkezik, nincs művelet szükség ügyfél kulcstároló engedélyezéséhez. Ha ez a művelet van szükség, amely a szervezet egyik be egy támogatási jegyet haladnak kezdeményezik egy Microsoft-mérnök ügyfél kulcstároló kérelmek.
