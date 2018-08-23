---
title: Az Azure Service Fabric-fürt biztonságos kapcsolatok konfigurálása |} A Microsoft Docs
description: Útmutató az Azure Service Fabric-fürt által támogatott biztonságos kapcsolatok konfigurálása a Visual Studio használatával.
services: service-fabric
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: tglee
ms.assetid: 80501867-dd7a-4648-8bd6-d4f26b68402d
ms.service: multiple
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: 8d76a2144234591792359ed8dd4a0779e6a2fc5c
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2018
ms.locfileid: "42060602"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Biztonságos kapcsolatok konfigurálása a Service Fabric-fürtön a Visual Studióból
Ismerje meg, hogyan használható a Visual Studio biztonságosan elérni az Azure Service Fabric-fürt a beállított hozzáférés-vezérlési házirendeket.

## <a name="cluster-connection-types"></a>Fürt kapcsolattípusok
Az Azure Service Fabric-fürt által támogatott kétféle kapcsolatot: **nem biztonságos** kapcsolatok és **tanúsítványalapú x509** biztonságos kapcsolódás. (Service Fabric-fürtök a helyszínen üzemeltetett **Windows** és **dSTS** hitelesítések is támogatottak.) Konfigurálja a fürt kapcsolati típust, a fürt létrehozásakor kell. Miután létrejött, a kapcsolat típusa nem módosítható.

A Visual Studio Service Fabric-eszközök az összes hitelesítési típusok támogatása a közzétételre fürthöz csatlakozik. Lásd: [beállítása a Service Fabric-fürtön az Azure Portalról](service-fabric-cluster-creation-via-portal.md) biztonságos Service Fabric-fürt beállításához útmutatást.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Fürt-kapcsolatok konfigurálása a profilok közzététele
Ha közzétesz egy Service Fabric-projekt a Visual Studióból, használja a **Service Fabric-alkalmazás közzététele** párbeszédpanelen válassza ki az Azure Service Fabric-fürt. A **kapcsolati végpont**, jelölje be az előfizetéshez tartozó meglévő fürthöz.

![A ** közzététele Service Fabric alkalmazás ** párbeszédpanelen a Service Fabric-kapcsolat konfigurálására szolgál.][publishdialog]

A **Service Fabric-alkalmazás közzététele** párbeszédpanel automatikusan ellenőrzi a fürt kapcsolatot. Ha a rendszer kéri, jelentkezzen be az Azure-fiókjával. Ha az ellenőrzés eredménye, az azt jelenti, hogy a rendszer rendelkezik a megfelelő tanúsítványokat a telepítés után biztonságosan csatlakozhat a fürthöz, vagy a fürt nem biztonságos. Érvényesítési hibák okozhatja hálózati probléma, vagy nem rendelkezik a rendszer megfelelően konfigurálva a biztonságos fürthöz való csatlakozáshoz.

![A ** közzététele Service Fabric alkalmazás ** párbeszédpanelen ellenőrzi, hogy egy meglévő megfelelően konfigurálva a Service Fabric-fürt kapcsolat.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Egy biztonságos fürthöz való csatlakozáshoz
1. Ellenőrizze, hogy az ügyféltanúsítványokat, amely megbízik a célul szolgáló fürtcsomópont hozzáférhet. A tanúsítvány általában a személyes információcsere (.pfx) fájlként van osztva. Lásd: [beállítása a Service Fabric-fürtön az Azure Portalról](service-fabric-cluster-creation-via-portal.md) számára a hozzáférést biztosítani az ügyfél-kiszolgáló konfigurálása.
2. Telepítse a megbízható tanúsítvány. Ehhez kattintson duplán a .pfx-fájlt, vagy a PowerShell-szkripttel az Import-PfxCertificate a tanúsítványok importálásához. Telepítse a tanúsítványt a **Cert: \LocalMachine\My**. Beleegyezem, fogadja el az összes alapértelmezett beállítást tanúsítvány importálása közben.
3. Válassza ki a **közzététele...**  parancsot a helyi menüben nyissa meg a projekt a **Azure-alkalmazások közzététele** párbeszédpanel, és válassza ki a cél fürtnek. Az eszköz automatikusan oldja fel a kapcsolatot, és menti a biztonságos kapcsolat paramétereit a közzétételi profilt.
4. Választható lehetőség: Szerkesztheti a közzétételi profilt, a biztonságos fürthöz kapcsolat megadásához.
   
   Manuálisan szerkeszti a közzétételi profil XML-fájlt adja meg a tanúsítvány adatait, feltétlenül ellenőrizze, hogy a tanúsítványtároló-nevet, mivel tárolja a hely és a tanúsítvány ujjlenyomatát. Adja meg ezeket az értékeket a tanúsítványtároló neve és a tároló helye kell. Lásd: [hogyan: a tanúsítvány ujjlenyomatának lekérését](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx) további információt.
   
   Használhatja a *ClusterConnectionParameters* paraméterek használatával adja meg a Service Fabric-fürtön való csatlakozáskor használni kívánt PowerShell-paramétereket. Érvényes paramétereket a rendszer minden olyan, amely fogadja el a Connect-ServiceFabricCluster parancsmag. Lásd: [Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster) elérhető paraméterek listáját.
   
   Ha egy távoli fürtön használt közzététele, adja meg a megfelelő paramétereket, hogy a fürt szeretné. Az alábbiakban látható egy példa nem biztonságos fürthöz csatlakozik:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Íme egy példa egy x509 csatlakozik az ügyféltanúsítvány-alapú biztonságos fürthöz:
   
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
5. A további szükséges beállításokat, például a frissítési paraméterek és Parametr Aplikace fájl helye, szerkesztheti, és tegye közzé az alkalmazást a **Service Fabric-alkalmazás közzététele** párbeszédpanel a Visual Studióban.

## <a name="next-steps"></a>További lépések
Service Fabric-fürtök elérésével kapcsolatos további információkért lásd: [a fürt megjelenítése a Service Fabric Explorer használatával](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
