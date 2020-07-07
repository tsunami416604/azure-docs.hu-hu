---
title: Microsoft Azure Ügyfélszéf
description: A Microsoft Azure Ügyfélszéf technikai áttekintése, amely lehetővé teszi a felhőalapú szolgáltatói hozzáférés szabályozását, ha a Microsoftnak szüksége lehet az ügyféladatok elérésére.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/04/2019
ms.openlocfilehash: 5330c751aaa3fcbd5c7fc268e4a4de08d336d474
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82735436"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Microsoft Azure Ügyfélszéf

> [!NOTE]
> Ennek a funkciónak a használatához a szervezetnek rendelkeznie kell egy minimális **fejlesztői**szintű [Azure-támogatási csomaggal](https://azure.microsoft.com/support/plans/) .

A Ügyfélszéf for Microsoft Azure egy felületet biztosít az ügyfelek számára az ügyféladatok hozzáférési kérelmeinek áttekintéséhez és jóváhagyásához vagy elutasításához. Ez olyan esetekben használatos, amikor egy Microsoft mérnöknek egy támogatási kérelem során hozzá kell férnie az ügyféladatok eléréséhez.

Ez a cikk a Ügyfélszéf kérelmek kezdeményezésének, nyomon követésének és a későbbi felülvizsgálatok és auditálások tárolásának módját ismerteti.

A Ügyfélszéf mostantól általánosan elérhető, és jelenleg engedélyezve van a távoli asztali hozzáférés a virtuális gépekhez.

## <a name="workflow"></a>Munkafolyamat

Az alábbi lépések egy Ügyfélszéf-kérelem tipikus munkafolyamatát ismertetik.

1. A munkahelyen valaki problémája van az Azure-munkaterheléssel.

2. Miután ez a személy elhárítja a problémát, de nem tudja kijavítani, megnyitnak egy támogatási jegyet a [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). A jegy hozzá van rendelve egy Azure ügyfélszolgálati szakemberhez.

3. Egy Azure-támogatási szakember áttekinti a szolgáltatási kérelmet, és meghatározza a probléma megoldásának következő lépéseit.

4. Ha a támogatási szakember nem tudja elhárítani a problémát a szabványos eszközök és telemetria használatával, a következő lépés az igény szerinti (JIT) hozzáférési szolgáltatás használatával emelt szintű engedélyek kérése. Ez a kérelem az eredeti támogatási szakembertől is származhat. Más mérnököktől is származhat, mert a problémát az Azure DevOps csapata is felhasználja.

5. Miután az Azure-mérnök elküldte a hozzáférési kérelmet, az igény szerinti szolgáltatás kiértékeli a kérelmet, figyelembe véve a következő tényezőket:
    - Az erőforrás hatóköre
    - Azt határozza meg, hogy a kérelmező izolált identitás-e vagy többtényezős hitelesítést használ-e
    - Engedélyek szintjei

    Az JIT-szabály alapján ez a kérelem tartalmazhatja a belső Microsoft-jóváhagyók jóváhagyását is. A jóváhagyó lehet például az ügyfél-támogatási érdeklődő vagy a DevOps-kezelő.

6. Ha a kérelemhez közvetlen hozzáférésre van szükség az ügyféladatok eléréséhez, a rendszer egy Ügyfélszéf kérelmet kezdeményez. Például távoli asztali hozzáférés az ügyfél virtuális géphez.

    A kérés most már egy **ügyfél által bejelentett** állapotban van, és az ügyfél jóváhagyására vár a hozzáférés megadása előtt.

7. Az ügyfél szervezetében az Azure-előfizetéshez tartozó [tulajdonosi szerepkörrel](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) rendelkező felhasználó e-mailt kap a Microsofttól, hogy értesítést kapjon a függőben lévő hozzáférési kérelemről. Ügyfélszéf kérelmek esetében ez a személy a kijelölt jóváhagyó.

    Példa e-mailre:

    ![Azure Ügyfélszéf – értesítő e-mail](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. Az e-mail-értesítés a Azure Portal **Ügyfélszéf** paneljére mutató hivatkozást tartalmaz. Ha ezt a hivatkozást használja, a kijelölt jóváhagyó bejelentkezik a Azure Portalba, és megtekintheti a szervezete által Ügyfélszéf függőben lévő kérelmeket:

    ![Azure Ügyfélszéf – Kezdőlap](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   A kérelem négy napig az ügyfél-várólistában marad. Ezt követően a hozzáférési kérelem automatikusan lejár, és a Microsoft-mérnökök nem kapnak hozzáférést.

9. A függőben lévő kérelem részleteinek beszerzéséhez a kijelölt jóváhagyó kiválaszthatja a **függőben lévő kérelmekből**származó kulcstároló-kéréseket:

    ![Azure Ügyfélszéf – a függőben lévő kérelem megtekintése](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. A kijelölt jóváhagyó azt is kiválaszthatja, hogy a **szolgáltatási kérelem azonosítója** megtekintse az eredeti felhasználó által létrehozott támogatási jegyre vonatkozó kérelmet. Ez az információ kontextust biztosít a Microsoft ügyfélszolgálata és a jelentett probléma előzményeihez. Például:

    ![Azure Ügyfélszéf – a támogatási jegyre vonatkozó kérelem megtekintése](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. A kérelem áttekintése után a kijelölt jóváhagyó kiválasztja a **jóváhagyás** vagy a **Megtagadás**lehetőséget:

    ![Azure Ügyfélszéf – válassza a jóváhagyás vagy a Megtagadás lehetőséget](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    A kijelölés eredményeképpen:
    - **Jóváhagyás**: a Microsoft mérnöke hozzáférést kap. A hozzáférés az alapértelmezett nyolc órás időszakra van megadva.
    - **Megtagadás**: a Microsoft mérnök emelt szintű hozzáférési kérelmét a rendszer elutasítja, és nem végez további műveletet.

Naplózási célokra az ebben a munkafolyamatban végrehajtott műveletek naplózása [Ügyfélszéf kérelmek naplófájljaiban](#auditing-logs)történik.

## <a name="auditing-logs"></a>Naplók naplózása

Ügyfélszéf naplókat a rendszer a tevékenységek naplójában tárolja. A Azure Portal a Ügyfélszéf kérelmekkel kapcsolatos naplózási információk megtekintéséhez válassza a **tevékenységek naplóit** . Szűrheti az egyes műveleteket, például a következőket:
- **Kulcstároló-kérelem megtagadása**
- **Kulcstároló-kérelem létrehozása**
- **Kulcstároló-kérelem jóváhagyása**
- **Kulcstároló-kérelem lejárata**

Lássunk erre egy példát:

![Azure Ügyfélszéf – tevékenységek naplói](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios-in-general-availability"></a>Támogatott szolgáltatások és forgatókönyvek általánosan elérhetővé

A következő szolgáltatások és forgatókönyvek jelenleg általánosan elérhetők a Ügyfélszéf számára.

### <a name="remote-desktop-access-to-virtual-machines"></a>Távoli asztali hozzáférés a virtuális gépekhez

A Ügyfélszéf jelenleg engedélyezve van a távoli asztali hozzáférési kérelmek virtuális gépekre való hozzáférése. A következő munkaterhelések támogatottak:
- Szolgáltatásként szolgáló platform (Pásti) – Azure Cloud Services (webes szerepkör és feldolgozói szerepkör)
- Infrastruktúra-szolgáltatás (IaaS) – Windows és Linux (csak Azure Resource Manager)
- Virtuálisgép-méretezési csoport – Windows és Linux

> [!NOTE]
> Ügyfélszéf nem támogatja a klasszikus IaaS-példányok használatát. Ha IaaS klasszikus példányokon futó számítási feladatokkal rendelkezik, javasoljuk, hogy a Klasszikusból a Resource Manager-alapú üzemi modellekbe telepítse át őket. Útmutatásért lásd: a [IaaS-erőforrások platform által támogatott áttelepítése klasszikusról Azure Resource Managerra](../../virtual-machines/windows/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Részletes naplók

A távoli asztal elérését érintő forgatókönyvek esetében a Windows-eseménynaplók segítségével áttekintheti a Microsoft mérnök által végrehajtott műveleteket. Az eseménynaplók összegyűjtéséhez és az adatok a munkaterületre való másolásához vegye fontolóra a Azure Security Center használatát elemzés céljából. További információ: [adatgyűjtés Azure Security Centerban](../../security-center/security-center-enable-data-collection.md).

## <a name="supported-services-and-scenarios-in-preview"></a>Az előzetes verzióban támogatott szolgáltatások és forgatókönyvek

A következő szolgáltatások jelenleg előzetes verzióban érhetők el a Ügyfélszéf számára:

- Azure Storage

- Azure SQL DB

- Azure Data Explorer

- Virtual Machines (mostantól a memóriaképek és a felügyelt lemezek hozzáférését is magában foglalja)

- Azure-előfizetés továbbítása

Ha engedélyezni szeretné a Ügyfélszéf a szervezete számára az előzetes verzióhoz, regisztráljon a [Ügyfélszéf az Azure nyilvános előzetes](https://aka.ms/customerlockbox/insiderprogram)verziójára.


## <a name="exclusions"></a>Kizárások

Ügyfélszéf kérelmeket a következő mérnöki támogatási helyzetekben nem indítja el a rendszer:

- Egy Microsoft-mérnöknek olyan tevékenységet kell végrehajtania, amely a szokásos működési eljárásokon kívül esik. Ha például nem várt vagy kiszámíthatatlan helyzetekben szeretné helyreállítani vagy visszaállítani a szolgáltatásokat.

- A Microsoft mérnöke a hibaelhárítás részeként fér hozzá az Azure platformhoz, és véletlenül hozzáfér az ügyféladatok eléréséhez. Az Azure hálózati csapat például olyan hibaelhárítást végez, amely egy hálózati eszközön lévő csomag rögzítését eredményezi. Ha azonban az ügyfél a továbbítás során titkosította az adatforgalmat, a mérnök nem tudja beolvasni az adatforgalmat.

## <a name="next-steps"></a>További lépések

A Ügyfélszéf automatikusan elérhető minden olyan ügyfél számára, akik [Azure-támogatási csomaggal](https://azure.microsoft.com/support/plans/) rendelkeznek, és ez a **fejlesztő**minimális szintű.

Ha jogosult támogatási csomaggal rendelkezik, a Ügyfélszéf engedélyezéséhez nincs szükség beavatkozásra. Ügyfélszéf a kérelmeket egy Microsoft-mérnök kezdeményezi, ha ez a művelet szükséges ahhoz, hogy egy támogatási jegyet lehessen benyújtani a szervezetében található valakitől.
