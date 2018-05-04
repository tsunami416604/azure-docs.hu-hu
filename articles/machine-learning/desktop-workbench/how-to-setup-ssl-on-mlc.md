---
title: SSL engedélyezése az Azure Machine Learning számítási (MLC) fürtön |} Microsoft Docs
description: Egy Azure Machine Learning számítási (MLC) fürt hívások pontozó SSL beállításával kapcsolatos útmutatás beolvasása
services: machine-learning
author: SerinaKaye
ms.author: serinak
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/24/2018
ms.openlocfilehash: 444803eeb77e79a8bfe3271ddf27bd428042c875
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="enable-ssl-on-an-azure-machine-learning-compute-mlc-cluster"></a>SSL engedélyezése az Azure Machine Learning számítási (MLC) fürtön 

Ezek az utasítások SSL beállítása a Machine Learning számítási (MLC) fürt hívások pontozó teszik lehetővé. 

## <a name="prerequisites"></a>Előfeltételek 

1. Egy olyan CNAME REKORDOT (DNS-név), amikor a valós idejű pontozási használata mellett dönt.

2. Hozzon létre egy *jelszó-mentes* adott CNAME rendelkező tanúsítványt.

3. Ha a tanúsítvány már nem a PEM-formátumba, átalakíthatja a PEM-eszközökkel, például a *openssl*.

Az Előfeltételek befejezése után meg kell két fájlt:

* A tanúsítvány, például egy fájl `cert.pem`. Győződjön meg arról, hogy a fájl a teljes tanúsítványlánc.
* A kulcs, például egy fájl `key.pem`



## <a name="set-up-an-ssl-certificate-on-a-new-acs-cluster"></a>Állítson be egy új ACS fürtön SSL-tanúsítvány

Az Azure Machine Learning parancssori felülettel futtassa a következő parancsot a `-c` kapcsolót, hogy hozzon létre egy ACS-fürthöz kapcsolódó SSL-tanúsítvány:

```
az ml env create -c -g <resource group name> -n <cluster name> --cert-cname <CNAME> --cert-pem <path to cert.pem file> --key-pem <path to key.pem file>
```


## <a name="set-up-an-ssl-certificate-on-an-existing-acs-cluster"></a>Az SSL-tanúsítvány egy meglévő ACS-fürt beállítása

Ha SSL nélküli létrehozott fürt céloz meg, egy tanúsítvány Azure PowerShell-parancsmagok használatával adhat meg: 

```
Set-AzureRmMlOpCluster -ResourceGroupName my-rg -Name my-cluster -SslStatus Enabled -SslCertificate $certValueInPemFormat -SslKey $keyValueInPemFormat -SslCName foo.mycompany.com
```

## <a name="map-the-cname-and-the-ip-address"></a>A CNAME és az IP-cím

A CNAME REKORDOT a Előfeltételek kiválasztott és az IP-címét a valós idejű előtér (FE) közötti leképezést létrehozni. Annak megállapításához, a FE IP-címét, az alábbi parancsot futtatva. A kimenet egy "nyilvános", amely tartalmazza a valós idejű fürt előtéri IP-címe nevű mező jeleníti meg. Tekintse meg a DNS-szolgáltatónál egy olyan rekordot beállításához használt CNAME REKORDOT a nyilvános IP-cím teljes Tartománynevét a utasításait.



## <a name="auto-detection-of-a-certificate"></a>A tanúsítvány automatikus észlelés 

Amikor hoz létre egy valós idejű webes szolgáltatás használja a "`az ml service create realtime`" paranccsal, az Azure Machine Learning az SSL állíthat be a fürt automatikusan észleli és automatikusan beállítja az pontozási URL-címet a kijelölt tanúsítvánnyal. 

A következő parancsot a tanúsítvány állapotának megtekintéséhez. Figyelje meg, hogy a "https" előtag a pontozás URI és a CNAME REKORDOT a gazdagép nevében. 

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
