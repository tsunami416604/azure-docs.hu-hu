---
title: Egyéni beállítások az App Service Environment-környezetek – Azure
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
ms.date: 01/16/2018
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 6463759dbd217cd054f838c09c7cfcf99a06aa2c
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54390822"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>App Service Environment-környezetek egyéni konfigurációs beállításai
## <a name="overview"></a>Áttekintés
Mivel az App Service-(ASE) környezetek egyetlen ügyfél számára elkülönített, vannak bizonyos konfigurációs beállítások, amelyek kizárólag az App Service Environmentet alkalmazhatók. Ez a cikk a különféle speciális egyéni beállításokra elérhető App Service Environment-környezetek dokumentumok.

Ha nem rendelkezik App Service-környezet, [App Service-környezet létrehozása](app-service-web-how-to-create-an-app-service-environment.md).

App Service Environment-környezet testreszabásokat is tárolhatja az új tömböt használatával **clusterSettings** attribútum. Ez az attribútum található a "Tulajdonságok" szótárban, a *hostingEnvironments* Azure Resource Manager-entitást.

A következő Resource Manager-sablon kódrészlet látható rövidítése a **clusterSettings** attribútum:

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

A **clusterSettings** attribútumot tartalmazhat egy Resource Manager-sablon frissítéséhez az App Service-környezet.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Az Azure erőforrás-kezelő használata az App Service-környezet frissítése
Másik megoldásként frissítheti az App Service-környezet használatával [Azure erőforrás-kezelő](https://resources.azure.com).  

1. Az erőforrás-kezelőben, nyissa meg a csomópont az App Service Environment (**előfizetések** > **resourceGroups** > **szolgáltatók**  >  **Microsoft.Web** > **hostingEnvironments**). Ezután kattintson az adott App Service-környezet, amelyet frissíteni szeretne.
2. Kattintson a jobb oldali ablaktáblában **olvasási/írási** kattintson a felső eszköztár interaktív szerkessze az erőforrás-kezelő engedélyezése.  
3. Kattintson a kék **szerkesztése** gombra, a Resource Manager-sablon szerkeszthető.
4. A jobb oldali ablaktábla alján görgessen. A **clusterSettings** attribútum értéke legalsó, amelyben adja meg, vagy frissítse az értéket.
5. Írja be (vagy másolja és illessze be) a kívánt konfigurációs értékek tömbje a **clusterSettings** attribútum.  
6. Kattintson a zöld **PUT** gombra, hogy a jobb oldali ablaktáblán, hogy az App Service-környezet a módosítás tetején található.

A módosítás elküldése, azonban az App Service Environment, a módosítás érvénybe léptetéséhez az előtérrendszerek számával nagyjából 30 percet vesz igénybe.
Például ha egy App Service Environment-környezet négy előtérrendszerek, eltarthat nagyjából két órát a konfigurációjának frissítése a Befejezés gombra. A konfiguráció módosításának tesszük elérhetővé, míg más a méretezési műveletek vagy konfiguráció-módosítási műveletek is igénybe vehet az App Service-környezet helyére.

## <a name="disable-tls-10-and-tls-11"></a>Tiltsa le a TLS 1.0-s és a TLS 1.1-es

Ha szeretne kezelni egy alkalmazás által alapon TLS-beállítások, akkor használhatja a mellékelt útmutató a [kényszerítése a TLS-beállítások](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl#enforce-tls-versions) dokumentáció. 

Ha szeretné letiltani a bejövő a TLS 1.0 és a TLS 1.1 tartozó összes forgalmat az ASE környezetben az alkalmazások, állíthatja be a következő **clusterSettings** bejegyzés:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

A beállítás neve szerint 1.0-t, de konfigurálásakor letiltja a TLS 1.0 és a TLS 1.1.

## <a name="change-tls-cipher-suite-order"></a>Módosítsa a TLS titkosító csomag sorrendje
Az ügyfelek egy másik kérdést akkor, ha azok módosíthatja a kiszolgáló által egyeztetett titkosítások listája, és ez módosításával érhető el a **clusterSettings** alább látható módon. Rendelkezésre álló titkosító csomagok listája lekérhetők [MSDN-cikkben](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> Hibás értékek vannak beállítva, a SChannel nem ismeri a titkosítócsomag, ha a kiszolgáló összes TLS-kommunikációt előfordulhat, hogy működni. Ebben az esetben kell eltávolítani a *FrontEndSSLCipherSuiteOrder* bejegyzést **clusterSettings** , és küldje el a frissített Resource Manager-sablon, állítsa vissza az alapértelmezett titkosítási csomagok beállítások.  Körültekintően használja ezt a funkciót.
> 
> 

## <a name="get-started"></a>Bevezetés
Az Azure rövid útmutató Resource Manager-sablon hely tartalmaz egy sablont, amely a alapdefinícióját [App Service-környezet létrehozása](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
