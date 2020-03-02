---
title: Az űrlap-felismerő minta címkézési eszközének üzembe helyezése
titleSuffix: Azure Cognitive Services
description: Ismerje meg a felügyelt tanulást segítő űrlap-felismerő minta címkéző eszköz különböző módszereit.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: pafarley
ms.openlocfilehash: fa419d7dd9668ac2ce8f2b0eb904117c7e22692d
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207843"
---
# <a name="deploy-the-sample-labeling-tool"></a>A minta feliratozási eszközének üzembe helyezése

Az űrlap-felismerő minta címkéző eszköze egy olyan alkalmazás, amely Docker-tárolóban fut. Hasznos felhasználói felületet biztosít, amellyel a felügyelt tanulás érdekében manuálisan címkézheti az űrlapos dokumentumokat. A [feliratokkal ellátott feliratok](./quickstarts/label-tool.md) azt mutatják be, hogyan futtathatja az eszközt a helyi számítógépen, amely a leggyakoribb forgatókönyv. 

Ebből az útmutatóból megtudhatja, hogyan helyezheti üzembe és futtathatja a minta feliratozási eszközt. 

## <a name="deploy-with-azure-container-instances"></a>Üzembe helyezés Azure Container Instances

A Label (címke) eszközt Docker Web App-tárolóban is futtathatja. Először [hozzon létre egy új webalkalmazás-erőforrást](https://ms.portal.azure.com/#create/Microsoft.WebSite) a Azure Portal. Töltse ki az űrlapot az előfizetés és az erőforráscsoport részleteivel. Adja meg a következő információkat a kötelező mezőkben:
* **Közzététel**: Docker-tároló
* **Operációs** rendszer Rendszer: Linux

A következő lapon adja meg a következő mezőket a Docker-tároló beállításához:

* **Beállítások**: egyetlen tároló
* **Rendszerkép forrása**: Azure Container Registry
* **Hozzáférés típusa**: nyilvános
* **Rendszerkép és címke**: MCR.microsoft.com/Azure-Cognitive-Services/Custom-Form/labeltool:Latest

Az alábbi lépések nem kötelezőek. Miután az alkalmazás befejezte a telepítést, futtathatja és elérheti a Label (címke) eszközt online.

### <a name="connect-to-azure-ad-for-authorization"></a>Kapcsolódás az Azure AD-hez az engedélyezéshez

Javasoljuk, hogy a webalkalmazást az Azure Active Director (HRE) szolgáltatáshoz kapcsolódjon, hogy csak a hitelesítő adataival rendelkező felhasználó jelentkezzen be, és használja az alkalmazást. Kövesse az [app Service alkalmazás konfigurálása](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) a HRE való kapcsolódáshoz című témakör utasításait.

## <a name="next-steps"></a>Következő lépések

Térjen vissza a [vonatra a feliratokkal](./quickstarts/label-tool.md) , amelyből megtudhatja, hogyan használhatja az eszközt a betanítási adat manuális címkézésére és a felügyelt tanulásra.
