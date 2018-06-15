---
title: Azure Service Fabric-fürt biztonságos kapcsolatok konfigurálása |} Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja az Azure Service Fabric-fürt által támogatott biztonságos kapcsolatokat a Visual Studio használatával.
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
ms.openlocfilehash: e2772cc2c59b93c7e523eaa0127dcf4ea0bc589e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208685"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Biztonságos kapcsolat konfigurálása a Service Fabric-fürt a Visual Studio eszközből
Útmutató: a Visual Studio használatával biztonságosan elérni az Azure Service Fabric-fürt beállított hozzáférés-vezérlési házirendeket.

## <a name="cluster-connection-types"></a>Fürt-kapcsolat típusai
Az Azure Service Fabric-fürt által támogatott két típusú kapcsolatok: **nem biztonságos** kapcsolatok és **tanúsítványalapú x509** kapcsolatok biztonságos. (A Service Fabric-fürtök helyben tárolt, **Windows** és **dSTS** hitelesítések is támogatottak.) Be kell állítania a fürt kapcsolódási típus, a fürt létrehozásakor. A már létrehozott, a kapcsolat típusa nem módosítható.

A Visual Studio Service Fabric eszközök összes hitelesítéstípussal támogatása csatlakozni a közzétételt a fürthöz. Lásd: [a Service Fabric-fürt beállítása az Azure-portálról](service-fabric-cluster-creation-via-portal.md) biztonságos Service Fabric-fürt beállításával kapcsolatos utasításokat.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Fürt kapcsolatok konfigurálása a profilok közzététele
Ha közzéteszi a Service Fabric-projektet, a Visual Studio, a **Fabric-alkalmazás közzététele** párbeszédpanelen válassza ki az Azure Service Fabric-fürt. A **csatlakozási végpont**, jelölje be az előfizetéshez tartozó meglévő fürt.

![A ** közzététele Service Fabric Application ** párbeszédpanel a Service Fabric-kapcsolat konfigurálására szolgál.][publishdialog]

A **Fabric-alkalmazás közzététele** párbeszédpanel automatikusan ellenőrzi a fürt kapcsolat. Ha a rendszer kéri, jelentkezzen be az Azure-fiókjával. Ha az ellenőrzés eredménye akkor megfelelő, az azt jelenti, hogy a rendszer rendelkezik a megfelelő tanúsítványok biztonságosan csatlakozzon a fürthöz telepítve, vagy a fürt nem biztonságos. Az érvényesítési hibák okozhatja hálózati probléma, vagy nem rendelkezik a rendszer kapcsolódik egy biztonságos fürthöz megfelelően konfigurálva.

![A ** közzététele Service Fabric alkalmazás ** párbeszédpanel ellenőrzi, hogy egy meglévő, megfelelően konfigurálva a Service Fabric-fürt kapcsolat.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>A biztonságos fürthöz való kapcsolódás
1. Ellenőrizze, hogy a célfürt Megbízhatóságok ügyféltanúsítványok hozzáférhet. A tanúsítvány általában megosztott, személyes információcsere (.pfx) fájlként. Lásd: [a Service Fabric-fürt beállítása az Azure-portálról](service-fabric-cluster-creation-via-portal.md) biztosítanak hozzáférést az ügyfél a kiszolgáló konfigurálásának.
2. A megbízható tanúsítvány telepítéséhez. Ehhez kattintson duplán a .pfx-fájlra, vagy a PowerShell parancsfájl Import-PfxCertificate segítségével importálja a tanúsítványokat. A tanúsítvány telepítése **Cert: \LocalMachine\My**. Az OK gombra, fogadja el az összes alapértelmezett beállítást a tanúsítvány importálása során is.
3. Válassza ki a **közzététele...**  parancs a helyi menüben a projekt megnyitása a **Azure-alkalmazás közzététele** párbeszédpanel megnyitásához, majd válassza ki a cél fürtnek. Az eszköz automatikusan oldja fel a kapcsolatot, és menti a közzétételi profil a biztonságos kapcsolat paramétereit.
4. Választható lehetőség: Szerkesztheti a közzétételi profilt, a fürt biztonságos kapcsolat megadásához.
   
   Manuálisan szerkesztése az közzététele profil XML-fájlban adja meg a tanúsítvány adatait, jegyezze fel a tanúsítványtároló-nevet, mivel tárolja a helyét, és a tanúsítvány ujjlenyomata. Adja meg ezeket az értékeket a tanúsítványtároló neve, és helyezze a helyre lesz szüksége. Lásd: [Útmutató: a tanúsítvány ujjlenyomatának beolvasása](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx) további információt.
   
   Használhatja a *ClusterConnectionParameters* adja meg a PowerShell-paramétereket a Service Fabric-fürt történő csatlakozás során használandó paramétereket. Érvényes paraméterei bármilyen, amely elfogadja a Connect-ServiceFabricCluster parancsmag. Lásd: [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) felhasználható paraméterek listáját.
   
   Ha most közzétételt távoli fürtre, meg kell adnia a megfelelő paramétereket, hogy a fürt. A következő egy példa egy nem biztonságos fürthöz csatlakozik:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Íme egy példa egy x509 való kapcsolódáshoz biztonságos fürt tanúsítványalapú:
   
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
5. Bármely más szükséges beállításokat, például a frissítési paraméterek és az alkalmazás paraméter fájl helye, szerkesztheti, és tegye közzé az alkalmazást a **Fabric-alkalmazás közzététele** párbeszédpanel a Visual Studióban.

## <a name="next-steps"></a>További lépések
Service Fabric-fürtök elérésével kapcsolatos további információkért lásd: [a fürt megjelenítése a Service Fabric Explorer használatával](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
