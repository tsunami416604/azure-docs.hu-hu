---
title: Az új fejlesztői portál elérése és testreszabása – Azure API Management | Microsoft Docs
description: Ismerje meg, hogyan használhatja az új fejlesztői portált API Management.
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
ms.openlocfilehash: 27d5dcc99db040036ba296911aa33d8a312bb23f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851515"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>Az új fejlesztői portál elérése és testreszabása az Azure-ban API Management

Ez a cikk bemutatja, hogyan érheti el az új Azure API Management fejlesztői portált. Végigvezeti a vizualizációs szerkesztő felhasználói felületén – a tartalmak hozzáadásával és szerkesztésével, valamint a webhely megjelenésének testreszabásával.

![Új API Management fejlesztői portál](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a> Előfeltételek

- Hajtsa végre a következő rövid útmutatót: [Hozzon létre egy Azure API Management-példányt](get-started-create-service-instance.md).
- Azure API Management-példány importálása és közzététele. További információ: [Importálás és közzététel](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> Az új fejlesztői portál jelenleg előzetes verzióban érhető el.

## <a name="managed-vs-self-hosted"></a>Felügyelt és saját üzemeltetésű verziók

A fejlesztői portált kétféleképpen is létrehozhatja:

- **Felügyelt verzió** – a portál szerkesztésével és testreszabásával, amely a API Management-példányba van beépítve, `<your-api-management-instance-name>.developer.azure-api.net`és az URL-címen keresztül érhető el.
- **Saját** üzemeltetésű verzió – a portál API Management-példányon kívüli üzembe helyezésével és önálló üzemeltetésével. Ezzel a módszerrel szerkesztheti a portált, és kiterjesztheti a megadott alapvető funkciókat. A részletekért és az utasításokért tekintse meg a GitHub-tárházat a [portál forráskódjának][1]használatával.

## <a name="managed-access"></a>A portál felügyelt verziójának elérése

Kövesse az alábbi lépéseket a portál felügyelt verziójának eléréséhez.

1. Nyissa meg a Azure Portal API Management Service-példányát.
1. Kattintson a felső navigációs sávban az **új fejlesztői portál (előzetes verzió)** gombra. Megnyílik a portál felügyeleti verzióját tartalmazó új böngésző lap. Ha első alkalommal éri el a portált, az alapértelmezett tartalom automatikusan kiépítve lesz.

## <a name="managed-tutorial"></a>A portál felügyelt verziójának szerkesztése és testreszabása

Az alábbi videóban bemutatjuk, hogyan szerkesztheti a portál tartalmát, testreszabhatja a webhely megjelenését, és közzéteheti a módosításokat.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="faq"></a>Gyakori kérdések

Ebben a szakaszban az új fejlesztői portál általános kérdéseivel kapcsolatos gyakori kérdésekre fogunk válaszolni. A saját üzemeltetésű verzióra vonatkozó kérdésekért tekintse meg a [GitHub-adattár wiki szakaszát](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-content-from-the-old-developer-portal-to-the-new-one"></a>Hogyan telepíthetek tartalmat a régi fejlesztői portálról az újat?

Nem. A portálok nem kompatibilisek.

### <a name="when-will-the-portal-become-generally-available"></a>Mikor lesz általánosan elérhető a portál?

A portál jelenleg előzetes verzióban érhető el, és a naptári év végén általánosan elérhető lesz (2019). Az előzetes verzió nem használható éles környezetben.

### <a name="will-the-old-portal-be-deprecated"></a>A régi portál elavult lesz?

Igen, az új szolgáltatás általánosan elérhetővé válása után elavulttá válik. Ha problémái vannak, emelje fel őket [egy dedikált GitHub-problémával](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>Az új portál rendelkezik a régi portál összes szolgáltatásával?

Az általános elérhetőség célja, hogy egy forgatókönyv-alapú szolgáltatási paritást biztosítson a régi portálon. Addig is előfordulhat, hogy az előzetes verzió nem rendelkezik a kiválasztott funkciókkal.

A kivételek a régi portálon *futó alkalmazások* és *problémák* , amelyek nem lesznek elérhetők az új portálon. Ha *problémákat* használ a régi portálon, és szükség van rájuk az újat, tegye fel a megjegyzést [egy dedikált GitHub](https://github.com/Azure/api-management-developer-portal/issues/122)-problémába.

### <a name="ive-found-bugs-andor-id-like-to-request-a-feature"></a>Hibákat és/vagy szolgáltatást szeretnék igényelni.

Remek! [A GitHub-tárház problémái című szakaszában](https://github.com/Azure/api-management-developer-portal/issues)megadhatja a visszajelzéseket, elküldheti a szolgáltatásra vonatkozó kéréseket, illetve hibajelentéseket is küldhet. Itt is Köszönjük visszajelzését a `community` címkével megjelölt problémákkal kapcsolatban.

### <a name="i-want-to-move-the-content-of-the-new-portal-between-environments-how-can-i-do-that-and-do-i-need-to-go-with-the-self-hosted-version"></a>Szeretném áthelyezni az új portál tartalmát a környezetek között. Hogyan tehetem ezt meg, és szükségem van a saját üzemeltetésű verzióra?

Ezt megteheti mind a portálon, mind a felügyelt, mind a saját üzemeltetésű kiszolgálón. Az új fejlesztői portál a API Management szolgáltatás felügyeleti API-jával támogatja a tartalmak kinyerését. Az API-kat a [GitHub-adattár wiki szakaszában](https://github.com/Azure/api-management-developer-portal/wiki/)dokumentáljuk. [Egy szkriptet](https://github.com/Azure/api-management-developer-portal/blob/master/scripts/migrate.bat)is írtunk, amely segíthet az első lépések megtételében.

Továbbra is dolgozunk ennek a folyamatnak a API Management DevOps Resource Kit-vel való összehangolásán.

### <a name="how-can-i-select-a-layout-when-creating-a-new-page"></a>Hogyan választhatok ki egy *elrendezést* új *lap*létrehozásakor?

A rendszer egy elrendezést alkalmaz egy lapra úgy, hogy az URL-sablonját a *lap* URL-címéhez társítja. A (z ) URL-sablonnal `/wiki/*` ellátott elrendezés például `/wiki/` a következő szegmenst tartalmazó összes *oldalra* lesz `/wiki/getting-started`alkalmazva:, `/wiki/styles`, stb.

### <a name="why-doesnt-the-interactive-developer-console-work"></a>Miért nem működik az interaktív fejlesztői konzol?

Valószínűleg a CORS kapcsolódik. Az interaktív konzol ügyféloldali API-kérést tesz elérhetővé a böngészőből. A CORS probléma megoldásához vegyen fel [egy CORS](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS) -szabályzatot az API (ok) ra. Megadhatja az összes paramétert manuálisan (például a forrást https://contoso.com) , vagy használhat helyettesítő karaktert `*` ).

## <a name="next-steps"></a>További lépések

További információ az új fejlesztői portálról:

- [GitHub-adattár a forráskódtal][1]
- [Útmutató a portál önálló üzemeltetéséhez][2]
- [A projekt nyilvános ütemterve][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects