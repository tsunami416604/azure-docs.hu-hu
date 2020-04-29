---
title: A Micro Focus Enterprise Server 4,0 és Enterprise Developer 4,0 telepítése az Azure-on | Microsoft Docs
description: Az IBM z/OS mainframe-alapú számítási feladatokat az Azure Virtual Machines (VM) Micro Focus fejlesztési és tesztkörnyezet használatával helyezheti át.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 03/30/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 5b3fa5faccf17df17bc4f7cc2d8b023b868fdbe1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80411196"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>A Micro Focus Enterprise Server 4,0 és Enterprise Developer 4,0 telepítése az Azure-ban

Ez a cikk bemutatja, hogyan állítható be a [Micro Focus Enterprise Server 4,0](https://www.microfocus.com/documentation/enterprise-developer/es30/) és a [Micro focus Enterprise Developer 4,0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) az Azure-ban.

Az Azure-ban gyakori számítási feladatok fejlesztési és tesztelési környezetek. Ez a forgatókönyv gyakori, mert így költséghatékony és könnyen üzembe helyezhető, és leszakítható. A vállalati kiszolgálóval a Micro Focus létrehozta az egyik legnagyobb nagyvállalati szolgáltatói platformot. A z/OS munkaterheléseket az Azure-ban olcsóbb x86 platformon futtathatja Windows vagy Linux rendszerű virtuális gépek használatával.

> [!NOTE]
> Hamarosan elérhető: útmutató a [Micro Focus Enterprise Server 5,0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) Azure-beli virtuális gépeken való beállításához.

A telepítő olyan Azure-beli virtuális gépeket használ, amelyek a Windows Server 2016 rendszerképet futtatják az Azure Marketplace-en, Microsoft SQL Server 2017 már telepítve van. Ez a beállítás a Azure Stackra is érvényes.

A vállalati kiszolgáló megfelelő fejlesztői környezete a Microsoft Visual Studio 2017-es vagy újabb verzióiban, illetve a Visual Studio Community (ingyenesen letölthető) vagy az Eclipse-ben futó vállalati fejlesztő. Ez a cikk bemutatja, hogyan telepítheti azt egy Windows Server 2016 rendszerű virtuális géppel, amely a Visual Studio 2017-es vagy újabb verziójával rendelkezik.

## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt tekintse át az alábbi előfeltételeket:

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

- A Micro Focus szoftver és egy érvényes licenc (vagy próbaverziós licenc). Ha már meglévő Micro Focus-ügyfelet használ, forduljon a Micro Focus képviselőjéhez. Ellenkező esetben [próbaverziót igényelhet](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

- Szerezze be a [vállalati kiszolgáló és a nagyvállalati fejlesztő](https://www.microfocus.com/documentation/enterprise-developer/#")dokumentációját.

> [!NOTE]
> Az ajánlott eljárás egy helyek közötti virtuális magánhálózati (VPN) alagút vagy egy Jumpbox beállítása, amely az Azure virtuális gépekhez való hozzáférés szabályozását teszi elérhetővé.

## <a name="install-enterprise-server"></a>Az Enterprise Server telepítése

1. A jobb biztonság és kezelhetőség érdekében érdemes lehet új erőforráscsoportot létrehozni ehhez a projekthez – például **RGMicroFocusEntServer**. Használja az Azure-beli név első részét az erőforrás típusának kiválasztásához, hogy könnyebb legyen a listában.

2. Virtuális gépet hoz létre. Az Azure Marketplace-en válassza ki a kívánt virtuális gépet és operációs rendszert. Egy ajánlott beállítás:

    - **Vállalati kiszolgáló**: válassza ki a ES2 v3 virtuális gépet (2 vCPU és 16 GB memóriával) a Windows Server 2016 és a SQL Server 2017 rendszerű számítógépeken. Ez a rendszerkép az Azure piactéren érhető el. A vállalati kiszolgáló a Azure SQL Database is használhatja.

    - **Enterprise Developer**: válassza ki a B2ms virtuális gépet (2 vCPU és 8 GB memóriával) a Windows 10 és a Visual Studio telepítve. Ez a rendszerkép az Azure piactéren érhető el.

3. Az **alapismeretek** szakaszban adja meg a felhasználónevét és a jelszavát. Válassza ki a virtuális gépekhez használni kívánt **előfizetést** és **helyet/régiót** . Válassza ki az erőforráscsoport **RGMicroFocusEntServer** .

4. Helyezze mindkét virtuális gépet ugyanabba a virtuális hálózatba, hogy kommunikálni tudjon egymással.

5. Fogadja el az alapértelmezett beállításokat a többi beállításnál. Jegyezze fel a virtuális gépek rendszergazdájához létrehozott felhasználónevet és jelszót.

6. A virtuális gépek létrehozása után nyissa meg a 9003, 86 és 80 bejövő portokat HTTP-re és 3389-re a vállalati kiszolgáló számítógépén és a 3389-on lévő RDP-hez a fejlesztői gépen.

7. Ha be szeretne jelentkezni a vállalati kiszolgáló virtuális gépre, Azure Portal válassza ki a ES2 v3 virtuális gépet. Nyissa meg az **Áttekintés** szakaszt, és válassza a **Kapcsolódás** lehetőséget egy RDP-munkamenet indításához. Jelentkezzen be a virtuális géphez létrehozott hitelesítő adatok használatával.

8. Az RDP-munkamenetből töltse be a következő két fájlt. Mivel a Windowst használja, a fájlokat áthelyezheti az RDP-munkamenetbe:

    - **es\_40. exe**, a vállalati kiszolgáló telepítési fájlja.

    - **mflic**, a megfelelő licencfájl – a vállalati kiszolgáló nem töltődik be anélkül.

9. A telepítés elindításához kattintson duplán a fájlra. Az első ablakban válassza ki a telepítési helyet, és fogadja el a végfelhasználói licencszerződést.

     ![Micro Focus Enterprise Server telepítő képernyő](media/01-enterprise-server.png)

     A telepítés befejezésekor a következő üzenet jelenik meg:

     ![Micro Focus Enterprise Server telepítő képernyő](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Frissítések keresése

A telepítés után ellenőrizze, hogy vannak-e további frissítések, mivel számos előfeltétel, például a Microsoft C++ Újraterjeszthető csomag és a .NET-keretrendszer telepítése a vállalati kiszolgálóval együtt történik.

### <a name="upload-the-license"></a>A licenc feltöltése

1. Indítsa el a Micro Focus-licencek felügyeletét.

2. \> Kattintson **a** **Micro Focus License Manager** \> - **licencek felügyelete**elemre, majd kattintson a **telepítés** lapra. Válassza ki a feltölteni kívánt licenc formátumát: egy licencfájl vagy egy 16 karakterből álló licenckód. Egy fájl esetében például a **licencfájl**területen keresse meg a korábban feltöltött **mflic** -fájlt a virtuális gépre, és válassza a **Licencek telepítése**lehetőséget.

     ![Micro Focus – licencek felügyelete párbeszédpanel](media/03-enterprise-server.png)

3. Ellenőrizze, hogy a vállalati kiszolgáló betöltődik-e. Próbálja meg elindítani a vállalati kiszolgáló felügyeleti helyét egy böngészőből az URL <http://localhost:86/> -cím használatával. A vállalati kiszolgáló felügyeleti lapja az ábrán látható módon jelenik meg.

     ![Vállalati kiszolgáló felügyelete lap](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Enterprise Developer telepítése a fejlesztői gépen

1. Válassza ki a korábban létrehozott erőforráscsoportot (például **RGMicroFocusEntServer**), majd válassza ki a fejlesztői rendszerképet.

2. A virtuális gépre való bejelentkezéshez nyissa meg az **Áttekintés** szakaszt, és válassza a **Kapcsolódás**lehetőséget. Ez a bejelentkezés egy RDP-munkamenetet indít el. Jelentkezzen be a virtuális géphez létrehozott hitelesítő adatok használatával.

3. Az RDP-munkamenetből töltse be a következő két fájlt (drag and drop, ha szeretné):

    - **edvs2017. exe**, a vállalati kiszolgáló telepítési fájlja.

    - **mflic**, a megfelelő licencfájl (Enterprise Developer nem töltődik be anélkül).

4. A telepítés elindításához kattintson duplán a **edvs2017. exe** fájlra. Az első ablakban válassza ki a telepítési helyet, és fogadja el a végfelhasználói licencszerződést. Ha szeretné, válassza a **Rumba 9,5 telepítése** lehetőséget a terminál-emulátor telepítéséhez, amelyre valószínűleg szüksége lesz.

     ![Micro Focus Enterprise Developer for Visual Studio 2017 telepítő párbeszédpanel](media/04-enterprise-server.png)

5. A telepítés befejezése után a következő üzenet jelenik meg:

     ![Sikeres telepítés üzenete](media/05-enterprise-server.png)

6. Indítsa el a Micro Focus License Managert ugyanúgy, mint a vállalati kiszolgáló esetében. \> Válassza a **Micro Focus License Manager** \> - **licencek felügyelete** **lehetőséget,** majd kattintson a **telepítés** fülre.

7. Válassza ki a feltölteni kívánt licenc formátum típusát: egy licencfájl vagy egy 16 karakterből álló licenckód. Egy fájl esetében például a **licencfájl**területen keresse meg a korábban feltöltött **mflic** -fájlt a virtuális gépre, és válassza a **Licencek telepítése**lehetőséget.

     ![Micro Focus – licencek felügyelete párbeszédpanel](media/07-enterprise-server.png)

A nagyvállalati fejlesztők betöltik a Micro Focus fejlesztési és tesztelési környezetének üzembe helyezését az Azure-ban.

## <a name="next-steps"></a>További lépések

- [A bank bemutató alkalmazás beállítása](./demo.md)
- [Vállalati kiszolgáló futtatása Docker-tárolókban](./run-enterprise-server-container.md)
- [Nagyszámítógépes alkalmazásmigrálás](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
