---
title: Tűzfal – Translator Text API mögötti fordítása
titlesuffix: Azure Cognitive Services
description: Lefordítja a Translator Text API IP-tűzfal mögötti.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: v-jansko
ms.openlocfilehash: bf1a63d996a1ed2021654c96576eb9dac2cff8e6
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52683363"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Hogyan lehet a Translator Text API IP-tűzfal mögötti fordítása

A Translator Text API a is tartománynév vagy IP-szűrés segítségével tűzfal mögötti fordítja le. Tartománynév szűrés legyen az előnyben részesített módszere. Hogy **nem ajánlott** tűzfal mögül IP-címet a Microsoft Translator futó szűrve. A telepítő valószínű, hogy a jövőben, előzetes értesítés nélkül megszüntetése. 

## <a name="translator-ip-addresses"></a>Translator IP-címek
Az IP-címek api.cognitive.microsofttranslator.com – 2018. November 20. a Microsoft Translator Text API:

* **Ázsia Csendes-óceáni:** 40.90.139.163, 104.44.89.44
* **Európa:** 40.90.138.4, 40.90.141.99
* **Észak-Amerika:** 40.90.139.36, 40.90.139.2


## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [A Translator API-hívás az IP-tűzfal mögötti fordítása](reference/v3-0-translate.md)