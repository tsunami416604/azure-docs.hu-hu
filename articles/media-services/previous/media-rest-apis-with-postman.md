---
title: Az Azure Media Services REST API-hívásokhoz Postman konfigurálása
description: Megtudhatja, hogyan Postman konfigurálása a Media Services REST API-hívásokat.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: juliako
ms.openlocfilehash: 72b110cac8d4945c958d760ff98e2da2f2796b62
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788262"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>A Media Services REST API-hívások Postman konfigurálása

Ez az oktatóanyag bemutatja, hogyan konfigurálhatja **Postman** így Azure Media Services (AMS) REST API-k hívására is használható. Az oktatóanyag bemutatja, hogyan környezet és a gyűjtés fájlok importálását **Postman**. A gyűjteményt, amely hívja az Azure Media Services (AMS) REST API-kat a HTTP-kérések csoportosított definíciókat tartalmazza. A környezeti változókat, amelyek a gyűjteményenként tartalmaz.

A környezet és a gyűjtemény cikkeket, amelyek bemutatják, hogyan különböző feladatokat az Azure Media Services REST API-k eléréséhez használatos.

## <a name="prerequisites"></a>Előfeltételek

- Telepítse a [Postman](https://www.getpostman.com/) többi ügyfél a REST API-k az AMS REST oktatóanyagok némelyike látható. 

    Használjuk **Postman** , de a többi eszközt lenne megfelelő. Más alternatív megoldások: **Visual Studio Code** , a többi beépülő modul vagy **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>A környezet konfigurálása 

1. Hozzon létre egy .JSON kiterjesztésű fájlt, amely tartalmazza a környezeti változók AMS oktatóanyagok szerepel. A fájl neve (például **AzureMediaServices.postman_environment.json**). Nyissa meg a fájlt, és illessze be a kódot, amely meghatározza a Postman környezet [a kód listaelem](postman-environment.md). 
2. Nyissa meg a **Postman**.
3. A képernyő jobb, jelölje ki a **kezelése környezet** lehetőséget.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-create-env.png)
4. Az a **kezelése környezet** párbeszédpanel, kattintson a **importálási**.
5. Keresse meg és jelölje ki a **AzureMediaServices.postman_environment.json** fájlt.
6. A **AzureMedia** környezet jelenik meg.
7. Zárja be a párbeszédpanelt.
8. Válassza ki a **AzureMedia** környezetben.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>A gyűjtemény konfigurálásához

1. Hozzon létre egy .JSON kiterjesztésű fájlt, amely tartalmazza a **Postman** , amelyek szükségesek ahhoz, hogy a fájl feltöltése a Media Services összes műveletet tartalmazó gyűjtemény. A fájl neve (például **AzureMediaServicesOperations.postman_collection.json**). Nyissa meg a fájlt, és illessze be a kódot, amely meghatározza a **Postman** gyűjteményben [a kód listaelem](postman-collection.md).
2. Kattintson a **importálása** a gyűjtemény fájl importálásához.
3. Válassza ki a **AzureMediaServicesOperations.postman_collection.json** fájlt.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>További lépések

Tekintse meg a [eszközök feltöltése](media-services-rest-upload-files.md) cikk.  
