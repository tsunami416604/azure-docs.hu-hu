---
title: Tűzfal – Translator Text API mögötti fordítása
titlesuffix: Azure Cognitive Services
description: Lefordítja a Translator Text API IP-tűzfal mögötti.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: 053bc5964644b4183572de35372e2580a80f19ae
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729984"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Hogyan lehet a Translator Text API IP-tűzfal mögötti fordítása

A Translator Text API a is tartománynév vagy IP-szűrés segítségével tűzfal mögötti fordítja le. Tartománynév szűrés legyen az előnyben részesített módszere. Hogy **nem ajánlott** tűzfal mögül IP-címet a Microsoft Translator futó szűrve. A telepítő valószínű, hogy a jövőben, előzetes értesítés nélkül megszüntetése.

## <a name="translator-ip-addresses"></a>Translator IP Addresses
The IP addresses for api.cognitive.microsofttranslator.com - Microsoft Translator Text API as of November 20, 2018:

* **Ázsia Csendes-óceáni:** 40.90.139.163, 104.44.89.44
* **Európa:** 40.90.138.4, 40.90.141.99
* **Észak-Amerika esetén:** 40.90.139.36, 40.90.139.2


## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [A Translator API-hívás az IP-tűzfal mögötti fordítása](reference/v3-0-translate.md)
