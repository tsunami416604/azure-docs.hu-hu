---
title: Biztonságos Azure Service Fabric-fürtkapcsolatok konfigurálása
description: Ismerje meg, hogyan konfigurálhatja az Azure Service Fabric-fürt által támogatott biztonságos kapcsolatokat a Visual Studio használatával.
author: cawaMS
ms.topic: conceptual
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: 11f76153726d3fc92118fb46cc61b4627ab6a1b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464093"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Biztonságos kapcsolatok konfigurálása a Visual Studio szolgáltatásfabric-fürtjeivel
Megtudhatja, hogyan használhatja a Visual Studio segítségével biztonságosan egy Azure Service Fabric-fürthöz konfigurált hozzáférés-vezérlési szabályzatokkal.

## <a name="cluster-connection-types"></a>Fürtkapcsolat-típusok
Az Azure Service Fabric-fürt kétféle kapcsolatot támogat: **nem biztonságos** kapcsolatokat és **x509-es tanúsítványalapú** biztonságos kapcsolatokat. (A helyszíni üzemeltetett Service Fabric-fürtök esetében a **Windows** és **a dSTS-hitelesítések** is támogatottak.) A fürt kapcsolattípusát a fürt létrehozásakor kell konfigurálnia. Létrehozása után a kapcsolat típusa nem módosítható.

A Visual Studio Service Fabric eszközei minden hitelesítési típust támogatnak a fürthöz való csatlakozáshoz a közzétételhez. A Biztonságos Service Fabric-fürt beállításával kapcsolatos útmutatásért olvassa [el a Service Fabric-fürt beállítása az Azure Portalról](service-fabric-cluster-creation-via-portal.md) című témakört.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Fürtkapcsolatok konfigurálása közzétételi profilokban
Ha egy Service Fabric-projektet tesz közzé a Visual Studio-ból, a **Service Fabric-alkalmazás közzététele** párbeszédpanelen válasszon egy Azure Service Fabric-fürtöt. A **Kapcsolat végpontja**csoportban jelöljön ki egy meglévő fürtöt az előfizetése alatt.

![A **Publish Service Fabric Application** párbeszédpanel a Service Fabric-kapcsolat konfigurálására szolgál.][publishdialog]

A **Szolgáltatásháló-alkalmazás közzététele** párbeszédpanel automatikusan ellenőrzi a fürtkapcsolatot. Ha a rendszer kéri, jelentkezzen be az Azure-fiókjába. Ha az érvényesítés sikeres, az azt jelenti, hogy a rendszer a megfelelő tanúsítványokkal rendelkezik a fürthöz való biztonságos csatlakozáshoz, vagy a fürt nem biztonságos. Az érvényesítési hibákat hálózati problémák okozhatják, vagy ha a rendszer nem megfelelően van beállítva egy biztonságos fürthöz való csatlakozáshoz.

![A **Publish Service Fabric Application** párbeszédpanel egy meglévő, helyesen konfigurált Service Fabric-fürtkapcsolatot ellenőriz.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Csatlakozás biztonságos fürthöz
1. Győződjön meg arról, hogy el tudja érni a célfürt által megbízhatónak adott ügyféltanúsítványok egyikét. A tanúsítvány általában személyes adatcserefájlként (.pfx) van megosztva. A [Service Fabric-fürt beállítása az Azure Portalról](service-fabric-cluster-creation-via-portal.md) című témakörben ismerteti, hogyan konfigurálhatja a kiszolgálót az ügyfél elérésének engedélyezéséhez.
2. Telepítse a megbízható tanúsítványt. Ehhez kattintson duplán a .pfx fájlra, vagy használja a PowerShell import-PfxCertificate parancsfájlt a tanúsítványok importálásához. Telepítse a **tanúsítványt a Tanúsítvány:\LocalMachine\My**mappába. A tanúsítvány importálása közben minden alapértelmezett beállítást elfogad.
3. Válassza a **Közzététel...** parancsot a projekt helyi menüjében az **Azure-alkalmazás közzététele** párbeszédpanel megnyitásához, majd válassza ki a célfürtöt. Az eszköz automatikusan feloldja a kapcsolatot, és menti a biztonságos kapcsolat paramétereit a közzétételi profilba.
4. Nem kötelező: A közzétételi profil szerkesztéséhez biztonságos fürtkapcsolatot adhat meg.
   
   Mivel a Tanúsítvány közzététele XML-fájlt manuálisan szerkeszti a tanúsítványadatok megadásához, mindenképpen jegyezze fel a tanúsítványtároló nevét, az üzlet helyét és a tanúsítvány ujjlenyomatát. Ezeket az értékeket meg kell adnia a tanúsítvány üzletnevéhez és tárolási helyéhez. További információ: [A tanúsítvány ujjlenyomatának lekérése.](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx)
   
   A *ClusterConnectionParameters* paraméterek segítségével megadhatja a PowerShell-paramétereket, amelyeket a Service Fabric-fürthöz való csatlakozáskor használ. Az érvényes paraméterek azok, amelyeket a Connect-ServiceFabricCluster parancsmag elfogad. Az elérhető paraméterek listáját a [Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster) oldalon található.
   
   Ha egy távoli fürtön tesz közzé, meg kell adnia az adott fürthöz tartozó megfelelő paramétereket. Az alábbi példa egy nem biztonságos fürthöz való csatlakozást mutat be:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Íme egy példa az x509-es tanúsítványalapú biztonságos fürthöz való csatlakozásra:
   
   ```xml
   <ClusterConnectionParameters
   ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
   X509Credential="true"
   ServerCertThumbprint="0123456789012345678901234567890123456789"
   FindType="FindByThumbprint"
   FindValue="9876543210987654321098765432109876543210"
   StoreLocation="CurrentUser"
   StoreName="My" />
   ```
5. Szerkessze az egyéb szükséges beállításokat, például a frissítési paramétereket és az alkalmazásparaméter fájlhelyét, majd tegye közzé az alkalmazást a Visual Studio **Szolgáltatásháló-alkalmazás közzététele** párbeszédpanelén.

## <a name="next-steps"></a>További lépések
A Service Fabric-fürtök eléréséről a [Fürt megjelenítése a Service Fabric Intéző vel](service-fabric-visualizing-your-cluster.md)című témakörben talál további információt.

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
