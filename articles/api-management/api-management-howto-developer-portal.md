---
title: Hozzáférés és az új fejlesztői portál – az Azure API Management testreszabása |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az új fejlesztői portál az API Management szolgáltatásban.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2019
ms.author: apimpm
ms.openlocfilehash: deb3fdf3069aaad4982d71806c209fe516e3c3d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66743575"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>Hozzáférés és az új, az Azure API Management fejlesztői portál testreszabása

Ez a cikk bemutatja, hogyan érhető el az új Azure API Management fejlesztői portálon. Végigvezeti a vizuális szerkesztő élmény - hozzáadása és szerkesztése a tartalom -, valamint a webhely megjelenésének testreszabásához.

![Új API Management fejlesztői portálon](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre a következő rövid útmutatót: [Az Azure API Management szolgáltatáspéldány létrehozása](get-started-create-service-instance.md).
- Importálása és közzététele az Azure API Management-példány. További információkért lásd: [importálása és közzététele](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> Az új fejlesztői portál jelenleg előzetes verzióban érhető el.

## <a name="managed-and-self-hosted-versions"></a>Felügyelt és a saját üzemeltetésű verziók

A fejlesztői portál kétféle módon hozhat létre:

- **Felügyelt verzió** – szerkesztésével, és a beépített portál testreszabása az API Management-példány és az URL-cím-n keresztül elérhető `<your-api-management-instance-name>.developer.azure-api.net`.
- **Helyi verzió** – üzembe helyezésével és önálló üzemeltető API Management-példány kívül a portálon. Ez a megközelítés lehetővé teszi, hogy a portálon szerkeszthetők kódbázis, és kiterjesztheti a megadott központi funkcióit. A részletek és az utasításokért tekintse meg a [forráskódját a portálon a GitHub-adattárának][1].

## <a name="access-the-managed-version-of-the-portal"></a>Hozzáférés a portál felügyelt verziója

A portál felügyelt verzió eléréséhez az alábbi lépésekkel.

1. Nyissa meg az API Management szolgáltatáspéldányt, az Azure Portalon.
1. Kattintson a **új fejlesztői portál (előzetes verzió)** gombot a felső navigációs sávban. A portál felügyeleti verzióját egy új böngészőlapon nyílik meg. Ha első alkalommal fér hozzá a portálon, az alapértelmezett tartalom automatikusan jön létre.

## <a name="edit-and-customize-the-managed-version-of-the-portal"></a>Szerkesztheti és testre szabhatja a portál felügyelt verziója

Az alábbi videóban bemutatjuk, hogyan szerkesztheti a tartalmat a portál, a webhely megjelenésének testreszabása és a módosítások közzétételére.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="next-steps"></a>További lépések

További információ az új fejlesztői portál:

- [GitHub-tárunkat, ahol a Forráskód][1]
- [A portálon önálló üzemeltetési útmutatást][2]
- [A projekt nyilvános ütemterve][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects