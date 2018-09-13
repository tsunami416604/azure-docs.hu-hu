---
title: SSL engedélyezése az Azure Machine Learning Compute (MLC) fürtön |} A Microsoft Docs
description: A kiértékelési hívások az Azure Machine Learning Compute (MLC) fürtön SSL beállításával kapcsolatos útmutatás beolvasása
services: machine-learning
author: SerinaKaye
ms.author: serinak
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/24/2018
ms.openlocfilehash: 982a6807ccaf393c3aea42f39f7e60bb7e0d3ac3
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645645"
---
# <a name="enable-ssl-on-an-azure-machine-learning-compute-mlc-cluster"></a>SSL engedélyezése az Azure Machine Learning Compute (MLC) fürtön 

Ezek az utasítások lehetővé teszik az SSL beállítása kiértékelési hívások egy Machine Learning Compute (MLC) fürtön. 

## <a name="prerequisites"></a>Előfeltételek 

1. Használata mellett dönt egy CNAME rekord (DNS-név) a valós idejű pontozási hívása esetén.

2. Hozzon létre egy *jelszó ingyenes* , hogy a CNAME-tanúsítványt.

3. Ha a tanúsítvány már nem PEM formátumú, átalakíthatja a PEM-eszközökkel például *openssl*.

Az Előfeltételek befejezése után két fájlt fog rendelkezni:

* A tanúsítvány, például egy fájl `cert.pem`. Ellenőrizze, hogy a fájl a teljes tanúsítványlánc.
* A kulcs, például egy fájl `key.pem`



## <a name="set-up-an-ssl-certificate-on-a-new-acs-cluster"></a>Egy SSL-tanúsítványa egy új ACS-fürt beállítása

Az Azure Machine Learning parancssori felület használatával futtassa a következő parancsot a `-c` kapcsolót, hogy SSL-tanúsítványt csatlakoztatott ACS-fürt létrehozása:

```
az ml env create -c -g <resource group name> -n <cluster name> --cert-cname <CNAME> --cert-pem <path to cert.pem file> --key-pem <path to key.pem file>
```


## <a name="set-up-an-ssl-certificate-on-an-existing-acs-cluster"></a>SSL-tanúsítvány, egy meglévő ACS-fürt beállítása

Ha SSL nélküli létrehozott fürt céloz meg, az Azure PowerShell-parancsmagok használatával új tanúsítványt is hozzáadhat.

Meg kell adnia a kulcs és a tanúsítvány nyers PEM formátumban. Ezek a PowerShell változókba olvashatók:

```
$keyValueInPemFormat = [IO.File]::ReadAllText('<path to key.pem file>')
$certValueInPemFormat = [IO.File]::ReadAllText('<path to cert.pem file>')
```
A tanúsítvány hozzáadása a fürthöz: 

```
Set-AzureRmMlOpCluster -ResourceGroupName my-rg -Name my-cluster -SslStatus Enabled -SslCertificate $certValueInPemFormat -SslKey $keyValueInPemFormat -SslCName foo.mycompany.com
```

## <a name="map-the-cname-and-the-ip-address"></a>A CNAME REKORDOT, és az IP-cím hozzárendelése

Hozzon létre egy leképezés a CNAME választotta az előfeltételeket és a valós idejű előtér (FE) IP-címe között. Fedezze fel az FE IP-címét, futtassa az alábbi parancsot. A kimenet mezője "publicIpAddress", amely tartalmazza a valós idejű fürt előtéri IP-címét jeleníti meg. Tekintse meg az utasításokat a DNS-szolgáltató az egy rekordot a nyilvános IP-címet a CNAME használt teljes Tartománynévvel.



## <a name="auto-detection-of-a-certificate"></a>A tanúsítvány automatikus felismerése 

Amikor egy létre valós idejű webes szolgáltatás a "`az ml service create realtime`" parancsot, az Azure Machine Learning az SSL beállítása a fürt automatikusan észleli és automatikusan beállítja a pontozás URL-CÍMÉT a kijelölt tanúsítvánnyal. 

A tanúsítvány állapotának megtekintéséhez futtassa a következő parancsot. Figyelje meg, hogy a pontozás URI-t és a CNAME REKORDOT a gazdagép nevében a "https" előtagot. 

``` 
az ml service show realtime -n <service name>
{
                "id" : "<your service id>",
                "name" : "<your service name >",
                "state" : "Succeeded",
                "createdAt" : "<datetime>”,
                "updatedAt" : "< datetime>",
                "scoringUri" : "https://<your CNAME>/api/v1/service/<service name>/score"
}
```
