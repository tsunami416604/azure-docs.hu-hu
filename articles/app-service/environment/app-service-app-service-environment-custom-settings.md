---
title: App Service környezetek egyéni beállításai – Azure
description: App Service környezetek egyéni konfigurációs beállításai
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2018
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: bf2441d5c0947ec94cbee247bdc4634ff9e53bfd
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070298"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>App Service környezetek egyéni konfigurációs beállításai
## <a name="overview"></a>Áttekintés
Mivel App Service környezetek (ASE-EK) egyetlen ügyfél számára vannak elkülönítve, bizonyos konfigurációs beállítások csak App Service környezetekre alkalmazhatók. Ez a cikk a App Service környezetekhez elérhető különböző testreszabásokat dokumentálja.

Ha nem rendelkezik App Service Environmentval, tekintse meg a [app Service Environment létrehozása](app-service-web-how-to-create-an-app-service-environment.md)című témakört.

App Service Environment testreszabásokat az új **clusterSettings** attribútumban található tömb használatával tárolhatja. Ez az attribútum a *hostingEnvironments* Azure Resource Manager entitás "Properties" szótárában található.

A következő rövidített Resource Manager-sablon a **clusterSettings** attribútumot jeleníti meg:

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

A **clusterSettings** attribútum egy Resource Manager-sablonban is szerepelhet a app Service Environment frissítéséhez.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>App Service Environment frissítése a Azure Erőforrás-kezelő használatával
Azt is megteheti, hogy a [Azure erőforrás-kezelő](https://resources.azure.com)használatával frissíti a app Service Environment.  

1. A erőforrás-kezelőban nyissa meg a app Service Environment (előfizetések > **resourceGroups** > **providers** > **Microsoft. Web**  >   **) csomópontját. hostingEnvironments**). Ezután kattintson a frissíteni kívánt App Service Environmentra.
2. A jobb oldali ablaktáblán kattintson az **olvasás/írás** lehetőségre a felső eszköztáron a erőforrás-kezelő interaktív szerkesztésének engedélyezéséhez.  
3. Kattintson a kék **Szerkesztés** gombra, hogy a Resource Manager-sablon szerkeszthető legyen.
4. Görgessen a jobb oldali ablaktábla aljára. A **clusterSettings** attribútum a legalul található, ahol megadhatja vagy frissítheti az értékét.
5. Írja be (vagy másolja és illessze be) a kívánt konfigurációs értékek tömbjét a **clusterSettings** attribútumba.  
6. Kattintson a jobb oldali ablaktábla tetején található zöld **put** gombra, hogy véglegesítse a módosítást a app Service Environment.

A módosítás elküldése azonban körülbelül 30 percet vesz igénybe, és megszorozza a változás életbe léptetéséhez szükséges App Service Environment számával.
Ha például egy App Service Environment négy előtérrel rendelkezik, a konfigurációs frissítés befejezéséhez körülbelül két órát vesz igénybe. A konfiguráció változása közben a rendszer nem végez más skálázási vagy konfigurációs módosítási műveletet a App Service Environment.

## <a name="disable-tls-10-and-tls-11"></a>A TLS 1,0 és a TLS 1,1 letiltása

Ha egy alkalmazáson keresztül szeretné kezelni a TLS-beállításokat, akkor használhatja a [TLS-beállítások érvényesítése](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl#enforce-tls-versions) dokumentációban ismertetett útmutatást. 

Ha le szeretné tiltani az összes bejövő TLS 1,0-és TLS 1,1-forgalmat egy kiegészítő csomag összes alkalmazásához, a következő **clusterSettings** -bejegyzést állíthatja be:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

A beállítás neve 1,0, de ha be van állítva, akkor a TLS 1,0 és a TLS 1,1 is letiltja.

## <a name="change-tls-cipher-suite-order"></a>TLS titkosítási csomag sorrendjének módosítása
Egy másik kérdés az ügyfelektől, ha módosíthatják a kiszolgáló által egyeztetett titkosítási algoritmusok listáját, és ez a **clusterSettings** az alább látható módon történő módosításával is megvalósítható. A rendelkezésre álló titkosítási csomagok listáját [ebből az MSDN](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx)-cikkből lehet lekérni.

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> Ha helytelen értékek vannak beállítva az SChannel által nem értelmezhető titkosítási csomaghoz, akkor a kiszolgálóval folytatott összes TLS-kommunikáció leállhat. Ebben az esetben el kell távolítania a *FrontEndSSLCipherSuiteOrder* bejegyzést a **clusterSettings** -ből, és el kell küldenie a frissített Resource Manager-sablont, hogy vissza lehessen térni az alapértelmezett titkosítási csomag beállításaihoz.  Körültekintően használja ezt a funkciót.
> 
> 

## <a name="get-started"></a>Bevezetés
Az Azure rövid útmutató Resource Manager-sablon webhelye tartalmaz egy [app Service Environment létrehozásához](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)szükséges alapdefiníciót tartalmazó sablont.

<!-- LINKS -->

<!-- IMAGES -->
