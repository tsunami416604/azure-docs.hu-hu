---
title: Webalkalmazás-oktatóanyag – ügyfélalkalmazás beállítása
description: Ez az oktatóanyag végigvezeti egy nyilvános alkalmazás regisztrálásának lépésein a webalkalmazások üzembe helyezésére való felkészüléshez
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: mihansen
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 58e21e46edfe1d03d42bf2202bcf1f22282631a9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "84872640"
---
# <a name="client-application-registration"></a>Ügyfélalkalmazás regisztrálása
Az előző oktatóanyagban üzembe helyezte és beállította az Azure API-t a FHIR-hez. Most, hogy már rendelkezik a FHIR-hez készült Azure API-val, regisztrálni fogjuk egy nyilvános ügyfélalkalmazás-alkalmazást. További részletekért és hibaelhárításért tekintse át a [nyilvános ügyfélalkalmazás teljes regisztrációját](register-public-azure-ad-client-app.md) ismertető útmutatót, de az alábbi oktatóanyag fő lépéseit ismertetjük.

1. Navigáljon Azure Active Directory
1. Válassza az **alkalmazás regisztrálása**  -->  **új regisztráció** lehetőséget
1. Nevezze el az alkalmazást, és állítsa be az átirányítási URI-t a következőrehttps://www.getpostman.com/oauth2/callback


![Ügyfélalkalmazás regisztrálása](media/tutorial-web-app/reg-public-app.png)

## <a name="client-application-settings"></a>Ügyfélalkalmazás beállításai
Az ügyfélalkalmazás regisztrálása után másolja az alkalmazás (ügyfél) AZONOSÍTÓját az Áttekintés lapról. Ezt az értéket később kell megadnia az ügyfélhez való hozzáféréskor.

![Alkalmazás AZONOSÍTÓjának másolása](media/tutorial-web-app/app-id.png)

Ezután állítsa be a megfelelő hitelesítési beállításokat. Válassza a **hitelesítés** lehetőséget a bal oldali oldalon. Győződjön meg a **hozzáférési jogkivonat** és az **azonosító jogkivonat** mezőiről. Azt is megteheti, hogy az átirányítási URI-t a felkészülés során is beállíthatja, amikor létrehozza a webalkalmazást az oktatóanyag negyedik részében. Ehhez adja hozzá \< a https://Web-App-NAME>. azurewebsites.net nevet az átirányítási URI-listához. Ha egy másik nevet választ a [webalkalmazás írásának](tutorial-web-app-write-web-app.md)lépése során, akkor vissza kell térnie, és frissítenie kell ezt.

![Alkalmazás-hitelesítési beállítások](media/tutorial-web-app/app-authentication.png)

Most, hogy beállította a megfelelő hitelesítést, állítsa be az API-engedélyeket. 
1. Válassza az **API-engedélyek** lehetőséget, majd kattintson **az engedély hozzáadása** lehetőségre.
1. A **szervezetem által használt API**-k alatt keressen rá az Azure Healthcare API-k kifejezésre
1. Válassza a **user_impersonation** lehetőséget, majd kattintson az **engedélyek hozzáadása** gombra.

## <a name="next-steps"></a>Következő lépések
Most már rendelkezik egy nyilvános ügyfélalkalmazás. A következő oktatóanyagban bemutatjuk a tesztelést és az alkalmazáshoz való hozzáférést a Poster használatával.

>[!div class="nextstepaction"]
>[Ügyfélalkalmazás tesztelése a Poster-ban](tutorial-web-app-test-postman.md)
