---
title: Absztrakt – Azure Functions és kiszolgáló nélküli platform biztonság
description: Absztrakt a Azure Functions és a kiszolgáló nélküli platform biztonsági tanulmányhoz.
author: TomShinder
ms.author: TomSh
ms.date: 06/21/2018
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.openlocfilehash: cf59f6343960a164be66ae27170d722eb26dc173
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727701"
---
# <a name="azure-functions-and-serverless-platform-security"></a>Azure Functions és kiszolgáló nélküli platform biztonsága
## <a name="abstract"></a>Absztrakt
A legtöbb vállalatnak jelentős mennyiségű erőforrásra és időre van szüksége a kiszolgálók kezeléséhez, ami a költségeket is felhasználja. Ha a vállalatok kevesebb erőforrást használnak a kiszolgálók kezeléséhez, a nagyszerű alkalmazások kiépítésére tudnak összpontosítani.  

A kiszolgáló nélküli számítástechnika csak ezt teszi lehetővé, mivel az alkalmazások futtatásához és méretezéséhez szükséges infrastruktúra kezelése az Ön számára történik. A kiszolgáló nélküli számítástechnika a kiszolgálók, az infrastruktúra és az operációs rendszerek absztrakciója. A kiszolgáló nélküli számítástechnika az eseményekre és triggerekre való reagálásra támaszkodik, amelyek mindegyike közel valós időben zajlik – a felhőben. 

Teljes körűen felügyelt szolgáltatásként a kiszolgálók kezelése és a kapacitás megtervezése láthatatlan a fejlesztő számára. A kiszolgáló nélküli keretrendszer segítségével Azure Functions használatával fejleszthet és telepíthet kiszolgáló nélküli alkalmazásokat. Ez egy parancssori felület (CLI), amely felépítést és automatizálást kínál a függvényekből és eseményekről származó kifinomult, eseményvezérelt, kiszolgáló nélküli architektúrák létrehozásához. Az Azure-függvény egy különálló üzembe helyezési egység, például egy szolgáltatás. Ez csupán a felhőben üzembe helyezett kód, amely leggyakrabban egyetlen feladatnak a végrehajtásához van írva.

Az előnyök ellenére a kiszolgáló nélküli biztonság saját kockázati tényezőket is tartalmaz a kezeléséhez. A kiszolgáló nélküli megközelítés nem vezet be új biztonsági szempontokat, de a meglévő biztonsági szempontokat is meg kell közelítenie. Ez a tanulmány a következő biztonsági szempontokra koncentrál: 
* A kiszolgáló nélküli platform előnyei
* A kiszolgáló nélküli számítástechnika biztonsági problémái
* Kritikus biztonsági problémák és enyhítések az Azure kontextusában
* A Microsoft kiszolgáló nélküli platform biztonságossá tétele

[A tanulmány letöltése](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-functions-serverless-platform-security/Microsoft%20Serverless%20Platform.pdf)

