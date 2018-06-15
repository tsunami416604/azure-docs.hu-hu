---
title: Az App Service Environment-környezetek egyéni beállítások
description: App Service Environment-környezetek egyéni konfigurációs beállításai
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/22/2016
ms.author: stefsch
ms.custom: mvc
ms.openlocfilehash: d60cdca78c143996fa5935726db0631321c9e2fe
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
ms.locfileid: "26129515"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>App Service Environment-környezetek egyéni konfigurációs beállításai
## <a name="overview"></a>Áttekintés
Mivel az App Service Environment-környezetek egyetlen ügyfél számára elkülönített, vannak bizonyos konfigurációs beállításainak kizárólag az App Service Environment-környezetek alkalmazható. Ez a cikk a különböző testreszabásokat App Service Environment-környezetek számára elérhető dokumentumok.

Ha még nem rendelkezik az App Service-környezetek, lásd: [egy App Service Environment-környezet létrehozása](app-service-web-how-to-create-an-app-service-environment.md).

Az új tömböt használatával tárolhatja az App Service Environment-környezet testreszabások **clusterSettings** attribútum. Ez az attribútum található a "Tulajdonságok" szótára a *hostingEnvironments* Azure Resource Manager entitás.

A következő rövidítése Resource Manager sablon kódrészletet mutat be a **clusterSettings** attribútum:

    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

A **clusterSettings** attribútum tartalmazhat egy Resource Manager-sablon frissítéséhez az App Service Environment-környezet.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Erőforrás-kezelővel Azure App Service-környezetek frissítése
Másik megoldásként frissítheti az App Service Environment-környezet használatával [Azure erőforrás-kezelő](https://resources.azure.com).  

1. Az erőforrás-kezelőben, nyissa meg a csomópontot az App Service Environment-környezet (**előfizetések** > **resourceGroups** > **szolgáltatók** > **Microsoft.Web** > **hostingEnvironments**). Kattintson az adott App Service-környezet, amely frissíti.
2. Kattintson a jobb oldali ablaktáblában **olvasási/írási** a felső eszköztár engedélyezése interaktív szerkesztése az erőforrás-kezelőben.  
3. Kattintson a kék **szerkesztése** gomb a Resource Manager-sablon szerkeszthető.
4. A jobb oldali ablaktábla alján görgessen. A **clusterSettings** attribútum legalsó, ahol adja meg, vagy frissítse az értéket.
5. Írja be (vagy másolja és illessze be) a kívánt konfigurációs tömböt a **clusterSettings** attribútum.  
6. Kattintson a zöld **PUT** gombra kattint, amely rendelkezik az App Service Environment-környezet a változtatás véglegesítése a jobb oldali ablaktábla tetején található.

A módosítás küldje el, de az App Service-környezet a változtatás érvénybe léptetéséhez előtér-webkiszolgálóinak számát szorozva körülbelül 30 percet vesz igénybe.
Például ha az App Service-környezetek négy előtér-webkiszolgálóinak, eltarthat körülbelül két órát a konfigurációs frissítés befejezéséhez. A konfigurációváltozás van megkezdődött, míg más skálázási műveletek vagy konfiguráció-módosítási műveletek nem történhet az App Service Environment-környezetben.

## <a name="disable-tls-10"></a>A TLS 1.0 letiltása
Ismétlődő kérdés az ügyfelektől, különösen az ügyfelek, akik a PCI-megfelelőséget foglalkoznak eseményeket, a explicit módon az alkalmazások a TLS 1.0 letiltása.

A TLS 1.0 a következő keresztül letiltható **clusterSettings** bejegyzést:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## <a name="change-tls-cipher-suite-order"></a>Módosítsa a TLS titkosító csomag sorrendje
-Ügyfél egy másik kérdést akkor, ha azok módosíthatja a kiszolgáló által egyeztetett titkosítási a listáját, és ez módosításával érhető el a **clusterSettings** alább látható módon. Rendelkezésre álló titkosító csomagok listája olvasható be a [MSDN-cikkben](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> Érvénytelen értékek vannak beállítva, a titkosítási csomagok, amelyek nem érti a SChannel a, ha a kiszolgáló minden TLS kommunikációs leállhat működését. Ebben az esetben kell eltávolítani a *FrontEndSSLCipherSuiteOrder* bejegyzést **clusterSettings** , és küldje el a frissített Resource Manager-sablon, ha vissza kíván térni az alapértelmezett titkosító csomag beállításokat.  Körültekintően használja ezt a funkciót.
> 
> 

## <a name="get-started"></a>Bevezetés
Az Azure gyors üzembe helyezés Resource Manager sablon hely tartalmazza az alapdefinícióját tartalmazó sablon [egy App Service Environment-környezet létrehozása](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
