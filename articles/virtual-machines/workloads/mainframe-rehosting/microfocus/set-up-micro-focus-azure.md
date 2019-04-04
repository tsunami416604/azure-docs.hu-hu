---
title: Micro fókusz Enterprise Server 4.0-s és 4.0-s vállalati fejlesztői telepítése Azure-ban |} A Microsoft Docs
description: Áthelyezési az IBM z/OS nagyszámítógép-alapú számítási feladatokat a Micro fókusz fejlesztési és tesztelési környezet az Azure-beli virtuális gépek (VM).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 33d0baf10df1882baf212c3e2c2683c8ca072fcc
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896436"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Micro fókusz Enterprise Server 4.0-s és 4.0-s vállalati fejlesztői telepítése Azure-ban

Ez a cikk bemutatja, hogyan állítható be [Micro fókusz Enterprise Server 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/) és [Micro fókusz vállalati fejlesztői 4.0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) az Azure-ban.

Gyakori számítási feladatok Azure-beli fejlesztési és tesztelési környezet, azért, mert így költséghatékony, könnyen üzembe vagy üzemen kívül. Vállalati kiszolgáló, a Micro fókusz hozott létre a legnagyobb nagyszámítógépes rehosting platformokra érhető el. Egy kevésbé költséges x86 z/OS számítási feladatokat futtathat az Azure-ban Windows vagy Linux rendszerű virtuális gépeken (VM) platform.

Ezt a beállítást használja a Microsoft SQL Server 2017 már telepítve van az Azure Marketplace-ről a Windows Server 2016 rendszerképet futtató Azure virtuális gépeken. A telepítő az Azure Stackben is vonatkozik.

Vállalati kiszolgáló megfelelő fejlesztői környezetben nagyvállalati fejlesztő, amely vagy a Microsoft Visual Studio 2017, a Visual Studio Community (ingyenes letöltése) fut, vagy az eclipse-ben. Ez a cikk bemutatja, hogyan telepítheti a Windows Server 2016 virtuális gép mellékelt telepítve a Visual Studio 2017 használatával.

## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt, tekintse meg az Előfeltételek:

- Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

- A Micro fókusz szoftvert és a egy érvényes licenccel (vagy próbaidőszakos díjcsomaghoz tartozó licenccel). Ha már Micro fókusz ügyfél, lépjen kapcsolatba a Micro fókusz képviselőjével. Ellenkező esetben [próbaverziója](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

- Az a dokumentáció eléréséhez [Enterprise Server és a vállalati fejlesztői](https://www.microfocus.com/documentation/enterprise-developer/#").

> [!NOTE]
> Ajánlott eljárás, hogy állítsa be egy helyek közötti virtuális magánhálózat (VPN) alagúton vagy a jumpbox, így szabályozhatja a hozzáférést az Azure virtuális gépekre.

## <a name="install-enterprise-server"></a>Vállalati kiszolgáló telepítése

1. Nagyobb biztonság és kezelhetőség, fontolja meg, és a projektet az új erőforráscsoport létrehozása – például **RGMicroFocusEntServer**. Az Azure-ban a név első része segítségével könnyebben észrevehetővé listáját az erőforrás típusának megadására.

2. Virtuális gépet hoz létre. Az Azure piactéren válassza ki a virtuális gép és a kívánt operációs rendszer. Íme a javasolt beállítás:

    - **Enterprise Server**: Válassza ki a ES2 v3 VM (2 Vcpu, 16 GB memóriával) rendelkező Windows Server 2016 és a telepített SQL Server 2017. Ez a rendszerkép az Azure piactéren érhető el. Vállalati kiszolgáló, valamint az Azure SQL Database használatával.

    - **Vállalati fejlesztői**: Válassza ki a B2ms virtuális gép (a 2 vcpu-k és 8 GB memória) a Windows 10-es és a Visual Studio telepítése. Ez a rendszerkép az Azure piactéren érhető el.

3. Az a **alapjai** panelen adja meg a felhasználónevét és jelszavát. Válassza ki a **előfizetés** és **hely vagy régió** szeretné a virtuális gépekhez való használatra. Válassza ki **RGMicroFocusEntServer** az erőforráscsoportnak.

4. Mindkét virtuális gép helyezze az azonos virtuális hálózatban, amelyekkel kommunikálhatnak egymással.

5. Az alapértelmezett értékeket a többi beállítás esetében fogadja el. Ne feledje, a felhasználónevet és jelszót, a rendszergazda ilyen virtuális gépet hoz létre.

6. A virtuális gépek létrehozása után nyissa meg a bejövő portokat 9003, 86 és a 80-as HTTP-hez és a vállalati kiszolgáló gépen RDP a 3389-es és a 3389-es, a fejlesztői gépen.

7. Jelentkezzen be a vállalati kiszolgáló virtuális géphez, az Azure Portalon, válassza ki a ES2 v3-as virtuális gép. Nyissa meg a **áttekintése** panelhez, és válassza **Connect** egy RDP-munkamenet elindításához. Jelentkezzen be a virtuális géphez létrehozott hitelesítő adatok használatával.

8. Az RDP-munkamenetből a következő két fájlt betölteni. Mivel Windows, így Ön húzással rendezheti át a fájlokat az RDP-munkamenetbe:

    - **es\_40. exe**, a vállalati kiszolgáló telepítőfájlt.

    - **mflic**, a megfelelő licencfájl – vállalati kiszolgáló, e nélkül nem tölthető be.

9. Kattintson duplán a fájlra, hogy elindítsa a telepítést. Az első ablakban a telepítés helyének kiválasztása, és fogadja el a végfelhasználói licencszerződést.

     ![Micro fókusz vállalati kiszolgáló beállítása képernyő](media/01-enterprise-server.png)

     A telepítő befejeződése után a következő üzenet jelenik meg:

     ![Micro fókusz vállalati kiszolgáló beállítása képernyő](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Frissítések ellenőrzése

A telepítés után mindenképpen óta előfeltételnek, mint például a Microsoft C++ újraterjeszthető csomag- és .NET-keretrendszer telepített vállalati kiszolgálóval együtt más frissítések keresése.

### <a name="upload-the-license"></a>Töltse fel a licenc

1. Indítsa el a Micro fókusz licenc felügyeleti.

2. Kattintson a **Start** \> **Micro fókusz License Manager** \> **licenc felügyeleti**, majd kattintson a **telepítése** fülre. Válassza ki a feltölteni a licenc-formátum: licencfájl vagy egy 16 karakterből álló licenckód. Például egy fájl a **licencfájl**, keresse meg a **mflic** a virtuális Gépet, majd válassza a korábban feltöltött fájl **licencek telepítése**.

     ![Micro fókusz licenc felügyeleti párbeszédpanel](media/03-enterprise-server.png)

3. Győződjön meg arról, hogy a vállalati kiszolgáló tölti be. Próbálja meg elindítani a vállalati kiszolgáló adminisztrációs hely, az URL-cím használatával közvetlenül a böngészőből <http://localhost:86/> . A vállalati kiszolgáló felügyelete lap látható módon jelenik meg.

     ![Vállalati kiszolgáló felügyelete lap](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>A fejlesztői gépen telepítse a vállalati fejlesztői

1. Válassza ki a korábban létrehozott erőforráscsoportot (például **RGMicroFocusEntServer**), majd válassza ki a fejlesztői lemezképet.

2. Jelentkezzen be a virtuális géphez, nyissa meg a **áttekintése** panelhez, és válassza **Connect**. Ezzel elindítja egy RDP-munkamenetet. Jelentkezzen be a virtuális géphez létrehozott hitelesítő adatok használatával.

3. Az RDP-munkamenetből betöltése a következő két fájlt (fogd és vidd tetszés szerint):

    - **edvs2017.exe**, a vállalati kiszolgáló telepítőfájlt.

    - **mflic**, a megfelelő licenc-fájlt (nagyvállalati fejlesztő fog betöltődni, e nélkül).

4. Kattintson duplán a **edvs2017.exe** fájlt, hogy elindítsa a telepítést. Az első ablakban a telepítés helyének kiválasztása, és fogadja el a végfelhasználói licencszerződést. Ha azt szeretné, válassza ki a **telepítése Rumba 9,5** telepítéséhez a terminálemulátorral, amely valószínűleg szüksége lesz.

     ![A Visual Studio 2017 telepítése párbeszédpanelen Micro fókusz nagyvállalati fejlesztő](media/04-enterprise-server.png)

5. A telepítő befejeződése után a következő üzenet jelenik meg:

     ![A telepítő sikeres üzenetek](media/05-enterprise-server.png)

6. Indítsa el a Micro fókusz Licenckezelőhöz, ahogy a vállalati kiszolgáló. Válassza a **Start** \> **Micro fókusz License Manager** \> **licenc felügyeleti**, és kattintson a **telepítése**fülre.

7. Válassza ki a feltölteni a licenc-formátum: licencfájl vagy egy 16 karakterből álló licenckód. Például egy fájl a **licencfájl**, keresse meg a **mflic** a virtuális Gépet, majd válassza a korábban feltöltött fájl **licencek telepítése**.

     ![Micro fókusz licenc felügyeleti párbeszédpanel](/edia/07-enterprise-server.png)

Vállalati fejlesztői betöltésekor Micro fókusz fejlesztési és tesztelési környezet az Azure-ban az üzembe helyezés befejeződött!

## <a name="next-steps"></a>További lépések

- [A Bank bemutató alkalmazás beállítása](./demo.md)
- [Enterprise Server-példányok Docker-tárolók futtatásához](./run-enterprise-server-container.md)
- [A nagyszámítógépes alkalmazások áttelepítése](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
