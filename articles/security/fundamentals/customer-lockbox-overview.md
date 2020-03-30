---
title: Ügyfélszéf a Microsoft Azure-hoz
description: Technikai áttekintés a Microsoft Azure ügyfélszéfjéről, amely biztosítja a felhőszolgáltatói hozzáférés szabályozását, amikor a Microsoftnak szüksége lehet az ügyféladatok elérésére.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/04/2019
ms.openlocfilehash: abc16ae7f7ab8bf15173248a6e7668e689e127de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561969"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Ügyfélszéf a Microsoft Azure-hoz

> [!NOTE]
> A funkció használatához a szervezetnek rendelkeznie kell egy [Azure-támogatási csomaggal,](https://azure.microsoft.com/support/plans/) amely minimális fejlesztői szinttel **rendelkezik.**

A Microsoft Azure ügyfélszéfje felületet biztosít az ügyfelek számára az ügyféladatok-hozzáférési kérelmek áttekintéséhez és jóváhagyásához vagy elutasításához. Olyan esetekben használatos, amikor a Microsoft mérnökének támogatási kérelem során hozzá kell férnie az ügyféladatokhoz.

Ez a cikk bemutatja, hogyan kezdeményezik, követik nyomon és tárolják az ügyfélszéf-kérelmeket a későbbi ellenőrzések és auditok érdekében.

Az Ügyfélszéf mostantól általánosan elérhető, és jelenleg engedélyezve van a virtuális gépek távoli asztali eléréséhez.

## <a name="workflow"></a>Munkafolyamat

A következő lépések egy ügyfélszéf-kérelem tipikus munkafolyamatát ismertetik.

1. Egy szervezetnél valakinek problémája van az Azure-munkaterheléssel.

2. Miután ez a személy elhárítja a problémát, de nem tudja kijavítani, megnyit egy támogatási jegyet az [Azure Portalról.](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac) A jegy egy Azure ügyfélszolgálati szakemberhez van rendelve.

3. Az Azure támogatási mérnöke áttekinti a szolgáltatási kérelmet, és meghatározza a probléma megoldásához szükséges következő lépéseket.

4. Ha a támogatási szakember nem tudja elhárítani a problémát a szabványos eszközök és telemetriai adatok használatával, a következő lépés az emelt szintű engedélyek kérése egy Just-In-Time (JIT) hozzáférési szolgáltatás használatával. Ez a kérés az eredeti támogatási szakembertől származhat. Vagy lehet, hogy egy másik mérnök, mert a probléma eszkalálódik az Azure DevOps csapat.

5. Miután az Azure Engineer elküldte a hozzáférési kérelmet, a Just-In-Time szolgáltatás kiértékeli a kérelmet, figyelembe véve az olyan tényezőket, mint például:
    - Az erőforrás hatóköre
    - Azt jelzi, hogy a kérelmező elszigetelt identitás-e vagy többtényezős hitelesítést használ-e
    - Engedélyszintek

    A jit-szabály alapján ez a kérelem a Microsoft belső jóváhagyóinak jóváhagyását is tartalmazhatja. A jóváhagyó lehet például az ügyfélszolgálati érdeklődő vagy a DevOps-kezelő.

6. Ha a kérelem közvetlen hozzáférést igényel az ügyféladatokhoz, ügyfélszéf-kérés tanuskul. Például távoli asztali hozzáférés az ügyfél virtuális gépéhez.

    A kérelem most egy **Ügyfél által értesített** állapotban van, és a hozzáférés megadása előtt az ügyfél jóváhagyására vár.

7. Az ügyfélszervezetnél az Azure-előfizetés [tulajdonosi szerepkört](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) használó felhasználó e-mailt kap a Microsofttól, amelyben értesíti őket a függőben lévő hozzáférési kérelemről. Az ügyfélszéf-kérelmek esetében ez a személy a kijelölt jóváhagyó.

    Példa e-mail re:

    ![Azure-ügyfélszéf – e-mailes értesítés](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. Az e-mail értesítés egy hivatkozást biztosít az Azure Portalon található **Ügyfél széf** panelre. Ezen a hivatkozáson a kijelölt jóváhagyó bejelentkezik az Azure Portalra, hogy megtekinthesse a szervezetük által az ügyféljegyzékhez benyújtott függőben lévő kérelmeket:

    ![Azure ügyfélszéf - céloldal](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   A kérelem négy napig marad a vevői várólistában. Ezt követően a hozzáférési kérelem automatikusan lejár, és a Microsoft mérnökei nem kapnak hozzáférést.

9. A függőben lévő kérelem részleteinek bekerüléséhez a kijelölt jóváhagyó kiválaszthatja a **függőben lévő kérelmekből**a széfkérelmet:

    ![Azure Ügyfélszéf - a függőben lévő kérelem megtekintése](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. A kijelölt jóváhagyó is kiválaszthatja a **SERVICE REQUEST id** megtekintéséhez a támogatási jegy kérelmet, amely az eredeti felhasználó által létrehozott. Ez az információ a Microsoft-támogatás szolgáltatásának és a jelentett probléma előzményeinek kontextusát tartalmazza. Példa:

    ![Azure Ügyfélszéf - tekintse meg a támogatási jegykérelmet](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. A kérelem áttekintése után a kijelölt jóváhagyó a **Jóváhagyás** vagy a Megtagadás lehetőséget **választja:**

    ![Azure Ügyfélszéf – válassza a Jóváhagyás vagy megtagadás lehetőséget](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    A kiválasztás eredményeként:
    - **Jóváhagyás**: A Microsoft mérnöke számára biztosított a hozzáférés. A hozzáférés egy alapértelmezett nyolc órás időtartamra biztosított.
    - **Megtagadás**: A Microsoft-mérnök emelt szintű hozzáférési kérelmet elutasít, és nincs további művelet.

Naplózási célokra az ebben a munkafolyamatban végrehajtott műveletek et a rendszer az [Ügyfélszéf kérelemnaplóiba](#auditing-logs)naplózza.

## <a name="auditing-logs"></a>Naplók naplózása

Az ügyfélszéf-naplók a tevékenységnaplókban tárolódnak. Az Azure Portalon válassza **a Tevékenységnaplók** lehetőséget az ügyfélszéf-kérelmekkel kapcsolatos naplózási információk megtekintéséhez. Szűrhet bizonyos műveletekre, például a következőkre:
- **Széfkérelem megtagadása**
- **Széfkérelem létrehozása**
- **Széfkérelem jóváhagyása**
- **Széfkérelem lejárata**

Lássunk erre egy példát:

![Azure-ügyfélszéf – tevékenységnaplók](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios-in-general-availability"></a>Támogatott szolgáltatások és forgatókönyvek általános elérhetősége

A következő szolgáltatások és forgatókönyvek jelenleg általánosan elérhetők az Ügyfélszéfhez.

### <a name="remote-desktop-access-to-virtual-machines"></a>Távoli asztali hozzáférés a virtuális gépekhez

Az Ügyfélszéf jelenleg engedélyezve van a virtuális gépekre irányuló távoli asztali hozzáférési kérelmekhez. A következő számítási feladatok támogatottak:
- Platform szolgáltatásként (PaaS) – Azure Cloud Services (webes szerepkör és feldolgozói szerepkör)
- Infrastruktúra szolgáltatásként (IaaS) – Windows és Linux (csak Azure Resource Manager esetén)
- Virtuális gép méretezési készlete - Windows és Linux

> [!NOTE]
> Az IaaS Classic példányokat a Customer Lockbox nem támogatja. Ha az IaaS Classic példányokon futó számítási feladatok, javasoljuk, hogy migrálja át őket a klasszikus erőforrás-kezelő telepítési modellek. További információt az [IaaS-erőforrások platformáltal támogatott áttelepítése klasszikusról Azure Resource Managerbe](../../virtual-machines/windows/migration-classic-resource-manager-overview.md)című témakörben talál.

#### <a name="detailed-audit-logs"></a>Részletes naplónaplók

A távoli asztali elérést tartalmazó esetekben a Windows eseménynaplói segítségével áttekintheti a Microsoft mérnöke által végrehajtott műveleteket. Fontolja meg az Azure Security Center használatával az eseménynaplók összegyűjtéséhez és az adatok elemzéséhez a munkaterületre másolásához. További információ: [Adatgyűjtés az Azure Security Centerben.](../../security-center/security-center-enable-data-collection.md)

## <a name="supported-services-and-scenarios-in-preview"></a>Támogatott szolgáltatások és forgatókönyvek előzetes verzióban

Az ügyfélszéf előzetes verzióban érhető el a következő szolgáltatások:

- Azure Storage

- Azure SQL DB

- Azure Data Explorer

- Virtuális gépek (most már a memóriaképekhez és a felügyelt lemezekhez való hozzáférést is lefedik)

- Azure-előfizetésátvitelek

Ha engedélyezni szeretné az ügyfélszéfet a szervezet ezen előzetes ajánlataihoz, regisztráljon [az Ügyfélszéf szolgáltatásra az Azure Nyilvános előzetes verzióhoz.](https://aka.ms/customerlockbox/insiderprogram)


## <a name="exclusions"></a>Kizárások

Az ügyfélszéf-kérelmek nem aktiválódnak a következő mérnöki támogatási forgatókönyvekben:

- A Microsoft mérnökének olyan tevékenységet kell végeznie, amely kívül esik a szabványos működési eljárásokon. Például a szolgáltatások helyreállítása vagy visszaállítása váratlan vagy előre nem látható esetekben.

- A Microsoft egyik mérnöke hibaelhárítási szolgáltatásként éri el az Azure platformot, és véletlenül hozzáfér az ügyféladatokhoz. Például az Azure Network Team olyan hibaelhárítást hajt végre, amely egy hálózati eszközön csomagrögzítést eredményez. Ha azonban az ügyfél titkosítja az adatokat az átvitel ideje alatt, a mérnök nem tudja olvasni az adatokat.

## <a name="next-steps"></a>További lépések

Az ügyfélszéf automatikusan elérhető minden olyan ügyfél számára, aki minimális **fejlesztői**szintű [Azure-támogatási csomaggal](https://azure.microsoft.com/support/plans/) rendelkezik.

Ha jogosult támogatási csomaggal rendelkezik, az Ügyfélszéf engedélyezéséhez nincs szükség műveletre. Az ügyfélszéf-kérelmeket a Microsoft egyik mérnöke kezdeményezi, ha ez a művelet szükséges a szervezet egy olyan támogatási jegyének alkalmazásához, amelyet a szervezet egy tagja nyújtott be.
