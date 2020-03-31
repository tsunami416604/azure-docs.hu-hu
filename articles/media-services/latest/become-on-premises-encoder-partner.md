---
title: Legyen helyszíni kódoló partner - Azure Media Services
description: Ez a cikk ismerteti, hogyan ellenőrizheti a helyszíni élő streamelő kódolók.
services: media-services
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 03/02/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: f98d9942f8c30f06b0144503b056c1e8a393ae52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298631"
---
# <a name="how-to-verify-your-on-premises-live-streaming-encoder"></a>A helyszíni élő streamelő kódoló ellenőrzése

Az Azure Media Services helyszíni kódoló partnereként a Media Services úgy népszerűsíti a terméket, hogy a kódolót a vállalati ügyfeleknek ajánlja. Ahhoz, hogy helyszíni kódoló partner ré váljon, ellenőriznie kell a helyszíni kódoló és a Media Services kompatibilitását. Ehhez végezze el az alábbi ellenőrzéseket.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="pass-through-live-event-verification"></a>Átmenő élő esemény ellenőrzése

1. Győződjön meg arról, hogy a Media Services-fiókban ellenőrizze, hogy a **streamelési végpont** fut-e. 
2. Hozza létre és indítsa el az **átmenő** élő eseményt. <br/> További információt az [Élő esemény állapotaés a számlázás](live-event-states-billing.md)című témakörben talál.
3. A betöltési URL-címek betöltése és a helyszíni kódoló konfigurálása az URL-cím használatával többbites élő közvetítésküldése a Media Services számára.
4. Az előnézeti URL-cím beérkezésével ellenőrizheti, hogy a kódoló bemenete ténylegesen meg érkezett-e.
5. Hozzon **Asset** létre egy új eszközobjektumot.
6. Hozzon létre egy **élő kimenetet,** és használja a létrehozott eszköznevet.
7. Hozzon létre egy **streamelési lokátort** a beépített **streamelési** szabályzattípusokkal.
8. Sorolja fel az elérési utakat a **streamelési lokátor,** hogy visszaszerezze az URL-eket használni.
9. A **streamelési végpont** állomásnevének beszereznie, amelyből streamelni szeretne.
10. Kombinálja a 8.
11. Futtassa az élő kódolót körülbelül 10 percig.
12. Állítsa le az élő eseményt. 
13. Egy lejátszó, például az [Azure Media Player](https://aka.ms/azuremediaplayer) segítségével megtekintheti az archivált eszközt, hogy a lejátszás ne legyen látható hiba minden minőségi szinten. Vagy nézze meg és érvényesítse az előnézeti URL-en keresztül az élő munkamenet során.
14. Rögzítse az eszközazonosítót, az élő archívum közzétett streamelési URL-címét, valamint az élő kódolóbeállításait és verzióját.
15. Állítsa alaphelyzetbe az élő esemény állapotát az egyes minták létrehozása után.
16. Ismételje meg az 5–15.

## <a name="live-encoding-live-event-verification"></a>Élő kódolás élő esemény ellenőrzése

1. Győződjön meg arról, hogy a Media Services-fiókban ellenőrizze, hogy a **streamelési végpont** fut-e. 
2. Hozza létre és indítsa el az **élő kódolásélő** eseményt. <br/> További információt az [Élő esemény állapotaés a számlázás](live-event-states-billing.md)című témakörben talál.
3. A betöltési URL-címek betöltése és a kódoló konfigurálása egy átviteli sebességű élő közvetítés leküldéses a Media Services.
4. Az előnézeti URL-cím beérkezésével ellenőrizheti, hogy a kódoló bemenete ténylegesen meg érkezett-e.
5. Hozzon **Asset** létre egy új eszközobjektumot.
6. Hozzon létre egy **élő kimenetet,** és használja a létrehozott eszköznevet.
7. Hozzon létre egy **streamelési lokátort** a beépített **streamelési** szabályzattípusokkal.
8. Sorolja fel az elérési utakat a **streamelési lokátor,** hogy visszaszerezze az URL-eket használni.
9. A **streamelési végpont** állomásnevének beszereznie, amelyből streamelni szeretne.
10. Kombinálja a 8.
11. Futtassa az élő kódolót körülbelül 10 percig.
12. Állítsa le az élő eseményt.
13. Egy lejátszó, például az [Azure Media Player](https://aka.ms/azuremediaplayer) segítségével megtekintheti az archivált eszközt, hogy a lejátszás ne legyen látható hiba minden minőségi szinten. Vagy nézze meg és érvényesítse az előnézeti URL-en keresztül az élő munkamenet során.
14. Rögzítse az eszközazonosítót, az élő archívum közzétett streamelési URL-címét, valamint az élő kódolóbeállításait és verzióját.
15. Állítsa alaphelyzetbe az élő esemény állapotát az egyes minták létrehozása után.
16. Ismételje meg az 5–15.

## <a name="longevity-verification"></a>Hosszú élettartam ellenőrzése

Kövesse ugyanazokat a lépéseket, mint az [élő események áthaladási ellenőrzésében,](#pass-through-live-event-verification) kivéve a 11. <br/>Ahelyett, hogy 10 perc, futtassa az élő kódoló egy hétig vagy tovább. Egy lejátszó, például az [Azure Media Player](https://aka.ms/azuremediaplayer) segítségével időről időre megtekintheti az élő közvetítést (vagy egy archivált eszközt), hogy a lejátszásnak ne legyenlátható hibája.

## <a name="email-your-recorded-settings"></a>E-mailben a rögzített beállítások

Végül küldje el e-mailben a rögzített beállításokat és az élő archiválási paramétereket az Azure Media Servicesnek, amshelp@microsoft.com hogy értesítse, hogy minden önellenőrzési ellenőrzés sikeres volt. Is, adja meg elérhetőségi adatait minden nyomon követése. A folyamattal kapcsolatos kérdéseivel felveheti a kapcsolatot az Azure Media Services csapatával.

## <a name="see-also"></a>Lásd még

[Tesztelt helyszíni kódolók](recommended-on-premises-live-encoders.md)

## <a name="next-steps"></a>További lépések

[Élő közvetítés a Media Services v3-as ával](live-streaming-overview.md)
