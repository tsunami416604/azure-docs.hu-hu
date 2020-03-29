---
title: A szintézis javítása az Egyéni hang - Beszédszolgáltatás segítségével
titleSuffix: Azure Cognitive Services
description: Custom Voice egy sor online eszközök, amelyek lehetővé teszik, hogy hozzon létre egy felismerhető, egy-az-egy-fajta hang a márka. Mindössze annyit kell tenni az induláshoz, hogy egy maroknyi audio fájlokat és a kapcsolódó átiratok. Az alábbi hivatkozásokra kattintva egyéni beszéd-szöveg élmény jön létre.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: dapine
ms.openlocfilehash: 786cf687e569af172eabfaa9068ae61ffd6b2988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220478"
---
# <a name="get-started-with-custom-voice"></a>Bevezetés a Custom Voice szolgáltatásba

[Custom Voice](https://aka.ms/customvoice) egy sor online eszközök, amelyek lehetővé teszik, hogy hozzon létre egy felismerhető, egy-az-egy-fajta hang a márka. Mindössze annyit kell tenni az induláshoz, hogy egy maroknyi audio fájlokat és a kapcsolódó átiratok. Az alábbi hivatkozásokra kattintva egyéni szövegfelolvasó élményt hozhat létre.

## <a name="whats-in-custom-voice"></a>Mi van a Custom Voice?

A Custom Voice megkezdése előtt szüksége lesz egy Azure-fiókra és egy beszédfelismerési szolgáltatás-előfizetésre. Miután létrehozott egy fiókot, előkészítheti az adatokat, betaníthatja és tesztelheti a modelleket, kiértékelheti a hangminőséget, és végső soron üzembe helyezheti az egyéni hangmodellt.

Az alábbi ábra kiemeli az egyéni hangmodell létrehozásának lépéseit az [Egyéni hangportál](https://aka.ms/customvoice)használatával. További információért használja a hivatkozásokat.

![Egyéni hangarchitektúradiagram](media/custom-voice/custom-voice-diagram.png)

1. [Iratkozzon fel és hozzon létre egy projektet](#set-up-your-azure-account) – Hozzon létre egy Azure-fiókot, és hozzon létre egy beszédszolgáltatási előfizetést. Ez az egyesített előfizetés hozzáférést biztosít a beszéd-szöveg, a szövegfelolvasás, a beszédfordítás és az egyéni hangportál hoz. Ezután a Beszédszolgáltatás-előfizetés használatával hozza létre az első Egyéni Hang-projektet.

2. [Adatok feltöltése](how-to-custom-voice-create-voice.md#upload-your-datasets) – Adatok (hang és szöveg) feltöltése az egyéni hangportálon vagy az Egyéni hang API-n keresztül. A portálon megvizsgálhatja és kiértékelheti a kiejtési pontszámokat és a jel-zaj arányokat. További információ: [Adatok előkészítése az Egyéni hanghoz](how-to-custom-voice-prepare-data.md).

3. [A modell betanítása](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – Az adatok segítségével hozzon létre egy egyéni szöveg-beszéd hangmodell. A modellt különböző nyelveken is betaníthatja. Betanítás után tesztelje a modellt, és ha elégedett az eredménnyel, telepítheti a modellt.

4. [A modell üzembe helyezése](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) – Hozzon létre egy egyéni végpontot a szövegfelolvasó hangmodellhez, és használja azt a termékekben, eszközökben és alkalmazásokban a beszédszintézishez.

## <a name="custom-neural-voices"></a>Egyéni neurális hangok

A neurális hang testreszabási képesség jelenleg nyilvános előzetes verzióban, a kiválasztott ügyfelek számára korlátozott. A kezdéshez töltse ki ezt a [jelentkezési lapot.](https://go.microsoft.com/fwlink/?linkid=2108737)

> [!NOTE]
> A Microsoft nak a felelős ai megtervezése iránti elkötelezettsége részeként célunk az egyének és a társadalom jogainak védelme, valamint az emberi és számítógépek közötti átlátható interakciók előmozdítása. Ebből az okból, egyéni neurális hang általában nem érhető el minden ügyfél számára. A technológiához csak az alkalmazások áttekintése után férhet hozzá, és elkötelezte magát amellett, hogy azt etikai elveinkkel összhangban használja. Tudjon meg többet [a jelentkezési gating](https://aka.ms/custom-neural-gating-overview)folyamatunkról.

## <a name="set-up-your-azure-account"></a>Az Azure-fiók beállítása

A beszédfelismerési szolgáltatás előfizetése szükséges, mielőtt az egyéni beszédportálon egyéni modell létrehozásához. Kövesse ezeket az utasításokat egy beszédszolgáltatási előfizetés létrehozásához az Azure-ban. Ha nem rendelkezik Azure-fiókkal, regisztrálhat egy újat.  

Miután létrehozott egy Azure-fiókot és egy Speech Service-előfizetést, be kell jelentkeznie az egyéni hangportálra, és csatlakoztatnia kell az előfizetést.

1. A beszédfelismerési szolgáltatás előfizetési kulcsának beolvasása az Azure Portalról.
2. Jelentkezzen be az [Egyéni hangportálra.](https://aka.ms/custom-voice)
3. Válassza ki az előfizetést, és hozzon létre egy beszédprojektet.
4. Ha másik beszédfelismerési előfizetésre szeretne váltani, használja a felső navigációs sávon található fogaskerék ikont.

> [!NOTE]
> A Custom Voice szolgáltatás NEM támogatja a 30 napos ingyenes próbakulcsot. A szolgáltatás használatához az Azure-ban létrehozott F0 vagy S0 kulccsal kell rendelkeznie.

## <a name="how-to-create-a-project"></a>Hogyan hozzunk létre egy projektet

Az egyéni hangportálon a tartalom, például az adatok, modellek, tesztek és végpontok **projektekbe** vannak rendezve. Minden projekt egy országra/nyelvre és a létrehozni kívánt hang nemére vonatkozik. Létrehozhat például egy női hangprojektet a call center csevegőrobotjai számára, amelyek angol nyelvet használnak az Egyesült Államokban (en-US).

Az első projekt létrehozásához jelölje be a **Szövegfelolvasás/Egyéni hang** lapot, majd kattintson az **Új projekt**gombra. A projekt létrehozásához kövesse a varázsló utasításait. A projekt létrehozása után négy lap jelenik meg: **Adatok,** **Betanítás,** **Tesztelés**és **Telepítés**. A [Következő lépések](#next-steps) című részben található hivatkozások segítségével megtudhatja, hogyan használhatja az egyes lapokat.

> [!IMPORTANT]
> Az [Egyéni hang portál](https://aka.ms/custom-voice) nemrég frissült! Ha korábbi adatokat, modelleket, teszteket és közzétett végpontokat hozott létre a CRIS.ai portálon vagy API-kkal, új projektet kell létrehoznia az új portálon, hogy ezekhez a régi entitásokhoz kapcsolódjon.

## <a name="next-steps"></a>További lépések

- [Egyéni hangadatok előkészítése](how-to-custom-voice-prepare-data.md)
- [Egyéni hang létrehozása](how-to-custom-voice-create-voice.md)
- [Útmutató: Hangminták rögzítése](record-custom-voice-samples.md)
