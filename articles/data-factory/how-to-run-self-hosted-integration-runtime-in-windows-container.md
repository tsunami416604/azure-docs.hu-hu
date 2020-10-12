---
title: Self-Hosted Integration Runtime futtatása a Windows-tárolóban
description: Tudnivalók a Self-Hosted Integration Runtime futtatásáról a Windows-tárolóban.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: d6f292ff89a70de90e6b86f19f73de26963d997f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87927563"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>Self-Hosted Integration Runtime futtatása a Windows-tárolóban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk azt ismerteti, hogyan futtatható Self-Hosted Integration Runtime a Windows-tárolóban.
A Azure Data Factory Self-Hosted Integration Runtime hivatalos Windows-tárolójának támogatását. Letöltheti a Docker-Build forráskódját, és összekapcsolhatja a saját folyamatos szállítási folyamatában lévő építési és üzemeltetési folyamatot. 

## <a name="prerequisites"></a>Előfeltételek 
- [A Windows-tárolóra vonatkozó követelmények](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker 2,3-es és újabb verziói 
- Self-Hosted Integration Runtime 4.11.7512.1 és újabb verzió 
## <a name="get-started"></a>Bevezetés 
1.  A Docker telepítése és a Windows-tároló engedélyezése 
2.  A forráskód letöltése: https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container
3.  Töltse le a legújabb verziót a "a" a "a"-ben 
4.  Nyissa meg a mappát a rendszerhéjban: 
```console
cd "yourFolderPath"
```

5.  Hozza létre a Windows Docker-rendszerképet: 
```console
docker build . -t "yourDockerImageName" 
```
6.  Docker-tároló futtatása: 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> A AUTH_KEY megadása kötelező ehhez a parancshoz. NODE_NAME, ENABLE_HA és HA_PORT nem kötelező. Ha nem állítja be az értéket, a parancs az alapértelmezett értékeket fogja használni. Az ENABLE_HA alapértelmezett értéke false, és a HA_PORT 8060.

## <a name="container-health-check"></a>Tároló állapotának ellenõrzése 
A 120 másodperces indítási időszak után az állapot-ellenőrzési szolgáltatás 30 másodpercenként rendszeresen fut. Megadja az IR állapotot a tároló motorjának. 

## <a name="limitations"></a>Korlátozások
Az alábbi funkciók jelenleg nem támogatottak Self-Hosted Integration Runtime Windows-tárolóban való futtatásakor:
- HTTP-proxy 
- Titkosított csomópont-csomópontos kommunikáció TLS/SSL-tanúsítvánnyal 
- Biztonsági másolat létrehozása és importálása 
- Daemon szolgáltatás 
- Automatikus frissítés 

### <a name="next-steps"></a>Következő lépések
- Tekintse át [az Integration Runtime fogalmait a Azure Data Factoryban](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime).
- Ismerje meg, hogyan [hozhat létre saját üzemeltetésű integrációs modult a Azure Portal](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).


