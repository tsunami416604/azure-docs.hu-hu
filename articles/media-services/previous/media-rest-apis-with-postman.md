---
title: Postás konfigurálása az Azure Media Services REST API-hívásaihoz
description: Ez a cikk a Postman a Media Services REST API-hívások konfigurálását ismerteti.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 11c9c26e7c0f36e1e3dba732e90a6aef95e6ee14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76694990"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Postás konfigurálása a Media Services v2 REST API-hívásaihoz  

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

Ez az oktatóanyag bemutatja, hogyan konfigurálhatja **a Postmant** az Azure Media Services (AMS) REST API-k hívásához. A bemutató bemutatja, hogyan kell importálni a környezetet és a gyűjtemény fájlokat **Postman**. A gyűjtemény az Azure Media Services (AMS) REST API-kat hívó HTTP-kérelmek csoportosított definícióit tartalmazza. A környezeti fájl azokat a változókat tartalmazza, amelyeket a gyűjtemény használ.

Ez a környezet és gyűjtemény olyan cikkekben használatos, amelyek bemutatják, hogyan valósíthat meg különböző feladatokat az Azure Media Services REST API-kkal.

## <a name="prerequisites"></a>Előfeltételek

- Telepítse a [Postman](https://www.getpostman.com/) REST-ügyfelet, hogy végrehajtsa az AMS REST oktatóanyagok egy részében látható REST API-kat. 

    A **Postmant** használjuk, de bármely egyéb REST-eszköz is megfelelő. Egyéb alternatívák: **Visual Studio Kód** a REST plugin vagy **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>A környezet konfigurálása 

1. Hozzon létre egy .json fájlt, amely az AMS oktatóanyagokban használt környezeti változókat tartalmazza. Nevezze el a fájlt (például **AzureMediaServices.postman_environment.json).** Nyissa meg a fájlt, és illessze be a Postman környezetet meghatározó kódot ebből a [kódlistából.](postman-environment.md) 
2. Nyissa meg a **Postmant**.
3. A képernyő jobb oldalán válassza a **Manage environment (Környezet felügyelete)** lehetőséget.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-create-env.png)
4. A **Manage environment (Környezet felügyelete)** párbeszédablakban kattintson az **Import (Importálás)** gombra.
5. Böngésszen és válassza ki az **AzureMediaServices.postman_environment.json** fájlt.
6. Az **AzureMedia-környezet** hozzáadva.
7. Zárja be a párbeszédpanelt.
8. Válassza ki az **AzureMedia** környezetben.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>A gyűjtemény konfigurálása

1. Hozzon létre egy .json fájlt, amely tartalmazza a **Postman** gyűjteményt, és tartalmazza a fájl Media Services szolgáltatásba való feltöltéséhez szükséges összes műveletet. Nevezze el a fájlt (például **AzureMediaServicesOperations.postman_collection.json).** Nyissa meg a fájlt, és illessze be a **postás** gyűjteményt meghatározó kódot ebből a [kódlistából.](postman-collection.md)
2. Kattintson az **Import (Importálás)** gombra a gyűjteményfájl importálásához.
3. Válassza ki az **AzureMediaServicesOperations.postman_collection.json** fájlt.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>További lépések

Tekintse meg a [feltöltési kellékek](media-services-rest-upload-files.md) ről szóló cikket.  
