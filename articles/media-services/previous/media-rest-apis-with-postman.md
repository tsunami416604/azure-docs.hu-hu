---
title: Postman konfigurálása az Azure Media Services REST API-hívások
description: Ismerje meg, a Media Services REST API-hívások a Postman konfigurálása.
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
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: 71f96420fe99ec230c9b25936fb6af4c993f615c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000984"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>A Media Services REST API-hívások a Postman konfigurálása  

Az oktatóanyag bemutatja, hogyan konfigurálhatja **Postman** így is használható az Azure Media Services (AMS) REST API-k meghívására. Az oktatóanyag bemutatja, hogyan környezet és a gyűjtemény fájlok importálását **Postman**. A gyűjtemény, amely az Azure Media Services (AMS) REST API-k hívása a HTTP-kérések csoportosított definíciókat tartalmazza. A környezeti változókat, amelyek a gyűjteményt tartalmaz.

A környezet és a gyűjtemény cikkek, amelyek bemutatják a különböző feladatok az Azure Media Services REST API-k eléréséhez használatos.

## <a name="prerequisites"></a>Előfeltételek

- Telepítse a [Postman](https://www.getpostman.com/) REST-ügyfelet, hogy végrehajtsa az AMS REST oktatóanyagok egy részében látható REST API-kat. 

    A **Postmant** használjuk, de bármely egyéb REST-eszköz is megfelelő. Egyéb választható lehetőségek: **A Visual Studio Code** az REST beépülő modullal vagy **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>A környezet konfigurálása 

1. A környezeti változókat az AMS oktatóanyagokban használt tartalmazó .json fájl létrehozásához. A fájl neve (például **AzureMediaServices.postman_environment.json**). Nyissa meg a fájlt, és illessze be a kódot, amely meghatározza a Postman környezetben [ez kódlista](postman-environment.md). 
2. Nyissa meg a **Postmant**.
3. A képernyő jobb oldalán válassza a **Manage environment (Környezet felügyelete)** lehetőséget.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-create-env.png)
4. A **Manage environment (Környezet felügyelete)** párbeszédablakban kattintson az **Import (Importálás)** gombra.
5. Keresse meg és válassza a **AzureMediaServices.postman_environment.json** fájlt.
6. A **AzureMedia** környezet kerül.
7. Zárja be a párbeszédpanelt.
8. Válassza ki a **AzureMedia** környezetben.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>A gyűjtemény konfigurálása

1. Hozzon létre egy .JSON kiterjesztésű fájlt, amely tartalmazza a **Postman** adatgyűjtés és a Media Services-fájl feltöltéséhez szükséges összes műveletet. A fájl neve (például **AzureMediaServicesOperations.postman_collection.json**). Nyissa meg a fájlt, és illessze be a kódot, amely meghatározza a **Postman** gyűjteményben [ez kódlista](postman-collection.md).
2. Kattintson az **Import (Importálás)** gombra a gyűjteményfájl importálásához.
3. Válassza ki a **AzureMediaServicesOperations.postman_collection.json** fájlt.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>További lépések

Tekintse meg a [adategységek feltöltése](media-services-rest-upload-files.md) cikk.  
