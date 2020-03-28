---
title: Egyéni beállítások konfigurálása
description: Konfigurálja a teljes Azure App Service-környezetre vonatkozó beállításokat. Ismerje meg, hogyan kell csinálni az Azure Resource Manager-sablonokkal.
author: stefsch
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: stefsch
ms.custom: mvc, seodec18
ms.openlocfilehash: 25393007a3cc878737ea5927cb65bcf7ef945313
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80057566"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Egyéni konfigurációs beállítások az App Service-környezetekhez
## <a name="overview"></a>Áttekintés
Mivel az App Service-környezetek (ASEs) egyetlen ügyfél től vannak elkülönítve, vannak bizonyos konfigurációs beállítások, amelyek kizárólag az App Service-környezetek alkalmazhatók. Ez a cikk az App Service-környezetek számára elérhető különböző testreszabásokat dokumentálja.

Ha nem rendelkezik App Service-környezettel, olvassa [el az App Service-környezet létrehozása című témakört.](app-service-web-how-to-create-an-app-service-environment.md)

Az App Service Environment testreszabásait az új **fürtbeállítások** attribútum tömbjének használatával tárolhatja. Ez az attribútum a *hostingEnvironments* Azure Resource Manager entitás "Tulajdonságok" szótárában található.

A következő rövidített Erőforrás-kezelő sablonkódrészlet a **fürtBeállítások** attribútumot jeleníti meg:

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

A **clusterSettings** attribútum szerepelhet egy Erőforrás-kezelő sablonban az App Service-környezet frissítéséhez.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>App Service-környezet frissítéséhez használja az Azure Resource Explorert
Azt is megteheti, hogy frissíti az App Service-környezetet az [Azure Resource Explorer](https://resources.azure.com)használatával.  

1. Az Erőforrás-kezelőben nyissa meg az App Service-környezet **(előfizetések** > **resourceGroups** > **szolgáltató** > **microsoft.web** > **hostingkörnyezet) csomópontját.** Ezután kattintson a frissíteni kívánt app szolgáltatási környezetre.
2. A jobb oldali ablaktáblában kattintson a felső eszköztár **Olvasás/írás** gombjára az interaktív szerkesztés engedélyezéséhez az Erőforrás-kezelőben.  
3. Az Erőforrás-kezelő sablon szerkesztéséhez kattintson a kék **Szerkesztés** gombra.
4. Görgessen a jobb oldali ablaktábla aljára. A **clusterSettings** attribútum a legalján található, ahol megadhatja vagy frissítheti annak értékét.
5. Írja be (vagy másolja és illessze be) a fürtbeállítások attribútumban a kívánt **konfigurációs** értékek tömbjét.  
6. Kattintson a jobb oldali ablaktábla tetején található zöld **PUT** gombra, és véglegesítse a módosítást az App Service-környezetben.

Azonban elküldi a módosítást, nagyjából 30 percet vesz igénybe, megszorozva az Alkalmazásszolgáltatás-környezet előtér-végződéseinek számával, hogy a módosítás érvénybe lépjen.
Ha például egy App Service-környezet négy előtér-végződést, nagyjából két órát vesz igénybe a konfigurációs frissítés befejezéséhez. A konfigurációmódosítása folyamatban van, az App Service-környezetben nem lehet más méretezési vagy konfigurációmódosítási műveleteket végrehajtani.

## <a name="enable-internal-encryption"></a>Belső titkosítás engedélyezése

Az App Service Environment fekete dobozrendszerként működik, ahol nem láthatók a belső összetevők vagy a kommunikáció a rendszeren belül. A nagyobb átviteli cím engedélyezéséhez a belső összetevők között alapértelmezés szerint nincs engedélyezve a titkosítás. A rendszer biztonságos, mivel a forgalom teljesen elérhetetlen a figyelés vagy az elérés szempontjából. Ha azonban rendelkezik olyan megfelelőségi követelménnyel, amely az adatelérési út teljes titkosítását igényli az egésztől a végéig, van egy módja annak, hogy ezt egy fürtbeállítással engedélyezze.  

        "clusterSettings": [
            {
                "name": "InternalEncryption",
                "value": "1"
            }
        ],
 
Miután az InternalEncryption clusterSetting engedélyezve van, hatással lehet a rendszer teljesítményére. Ha az InternalEncryption engedélyezéséhez módosítja a módosítást, az ASE instabil állapotban lesz, amíg a módosítás teljes mértékben propagálásra nem kerül. A módosítás teljes körű terjesztése eltarthat néhány órát, attól függően, hogy hány példány van az ASE-ben. Azt javasoljuk, hogy ne engedélyezze ezt az ASE használat közben. Ha engedélyeznie kell ezt egy aktívan használt ASE-n, javasoljuk, hogy a művelet befejezéséig irányítsa át a forgalmat egy biztonsági mentési környezetbe. 

## <a name="disable-tls-10-and-tls-11"></a>A TLS 1.0 és a TLS 1.1 letiltása

Ha a TLS-beállításokat alkalmazásonként szeretné kezelni, akkor használhatja a [TLS-beállítások kényszerítése](../configure-ssl-bindings.md#enforce-tls-versions) dokumentációban található útmutatást. 

Ha le szeretné tiltani az összes bejövő TLS 1.0-s és TLS 1.1-es forgalmat az ASE összes alkalmazásában, a következő **fürtbeállítások** bejegyzést állíthatja be:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

A beállítás neve 1.0, de ha be van állítva, letiltja mind a TLS 1.0, mind a TLS 1.1.

## <a name="change-tls-cipher-suite-order"></a>TLS titkosítási csomag sorrendjének módosítása
Egy másik kérdés az ügyfelektől, hogy módosíthatják-e a kiszolgálójuk által egyeztetett titkosítási listát, és ez a **fürtbeállítások** módosításával érhető el az alábbiak szerint. A rendelkezésre álló rejtjelcsomagok listája [ebből az MSDN-cikkből](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx)olvasható be.

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> Ha helytelen értékek vannak beállítva a titkosítási csomaghoz, amelyet az SChannel nem ért, előfordulhat, hogy a kiszolgálóval irányuló összes TLS-kommunikáció leáll. Ebben az esetben el kell távolítania a *FrontEndSSLCipherSuiteOrder* bejegyzést a **fürtbeállításokból,** és el kell küldenie a frissített Erőforrás-kezelő sablont, hogy visszatérjen az alapértelmezett titkosítási csomag beállításaihoz.  Kérjük, körültekintően használja ezt a funkciót.
> 
> 

## <a name="get-started"></a>Bevezetés
Az Azure Quickstart Resource Manager sablonwebhely tartalmaz egy sablont az Alapdefinícióval az [App Service-környezet létrehozásához.](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)

<!-- LINKS -->

<!-- IMAGES -->
