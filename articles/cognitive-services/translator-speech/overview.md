---
title: Diktálásfelismerési API fordítási dokumentáció |} Microsoft Docs
titleSuffix: Cognitive Services
description: Használja a Microsoft Translator beszéd fordítási API beszéd-beszéd átalakítás hozzáadása és az alkalmazások szöveg fordítási beszédfelismerés.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jansko
ms.openlocfilehash: 15f27e6b5b2fd7384958a660156855fc65f4e558
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347571"
---
# <a name="microsoft-translator-speech-api"></a>Microsoft Translator Speech API
A Microsoft Translator Diktálásfelismerési API-végpont, valós idejű, beszéd fordítások hozzáadása alkalmazások, eszközök és bármely, a többnyelvű beszéd fordítási függetlenül a cél operációs rendszer vagy a fejlesztési nyelveket igénylő megoldás használható. Az API-t mindkét beszédfelismerés beszéd és a szöveg fordítási beszédfelismerés használható.

A Microsoft Translator szöveg API az Azure-szolgáltatások, része a [Microsoft kognitív szolgáltatások API gyűjtemény](https://docs.microsoft.com/azure/#pivot=products&panel=cognitive) a gépi tanulási és a felhőben, könnyen használhatóvá a fejlesztési projektekben AI algoritmusokat.

A Microsoft Translator Diktálásfelismerési API-val ügyfélalkalmazások adatfolyam beszéd hang a szolgáltatáshoz, és adatfolyamon vissza szöveg - és hang-alapú eredmények, amelyek a felismert szöveg szerepel az adatforrás nyelvi és a fordítás a célként megadott nyelven. Szöveges eredményt automatikus beszéd felismerési (ASR) a bejövő hangadatfolyam mély Neurális hálózatokat technológiával alkalmazása hozzák létre. Nyers ASR kimeneti további jobban megfelelően szándékos felhasználói adat Igaz szöveg elnevezésű új technikát van javítja. Például Igaz szöveg eltávolítja disfluencies (a hmms és coughs), az ismétlődő szavak, és a visszaállítások megfelelő absztrakt és a nagybetűk. Maszkolnia, vagy zárja ki profanities is megtalálható. A megjelenítés és fordítási motorok kifejezetten képzett conversational beszéd kezelésére. 

A beszédfelismerés fordítási szolgáltatás csend észlelési egy utterance végén meghatározására használja. Hang tevékenységet megelőző szünet a szolgáltatás fogja adatfolyam vissza a befejezett utterance a végső eredményt. A szolgáltatás is küldhet vissza részleges eredmények, amelyek köztes elfpgads és egy utterance fordításainak folyamatban van. 

Beszéd-beszéd átalakítás fordításhoz a szolgáltatás teszi lehetővé a célként megadott nyelveken szóbeli szövegből (szöveg-beszéd) beszéd szintetizálásához. Szöveg-beszéd átalakítás hang létrejön az ügyfél által megadott formátumban. WAV és MP3 formátuma nem érhető el.

A beszédfelismerés fordítási API a WebSocket protokoll használatával az ügyfél és a kiszolgáló közötti kétirányú kommunikációs csatornát biztosítanak. 

## <a name="about-microsoft-translator"></a>Tudnivalók a Microsoft Translator
A Microsoft Translator egy olyan gépi fordítás felhőalapú szolgáltatás. Ez a szolgáltatás középpontjában van [fordító szöveg API] (https://www.microsoft.com/en-us/translator/translatorapi.aspx) és fordító Diktálásfelismerési API-kapcsolja a különböző Microsoft-termékek és szolgáltatások, és által használt vállalatok ezer világszerte, alkalmazások és munkafolyamatok, lehetővé téve a tartalmat egy globális közönség elérni.

További információ a [Microsoft Translator szolgáltatás](https://www.microsoft.com/en-us/translator/home.aspx)

## <a name="microsoft-translator-neural-machine-translation-nmt"></a>A Microsoft Translator Neurális gépi fordítás (NMT)
A Microsoft Translator Diktálásfelismerési API az örökölt statisztikai gépi fordítás (SMT) és az újabb Neurális gépi fordítás (NMT) használja a fordítások.

Statisztikai gépi fordítás platót javuló teljesítmény szempontjából. Általános rendszerekhez SMT jelentős egyáltalán nem javítása a fordítási minőségét. Új fordítási AI-alapú technológiát a Neurális hálózatokat (NN) alapú mérlegserpenyőre van eszközöket.

NMT biztosítja, nem csak a nyers fordítási minőségét szempontból is pontozási, mert az nagyobb fordítások hangkártya több Folyékonyan beszél, több emberi, mint SMT néhányat a meglévők közül. A kulcs a rugalmasság oka, hogy használja-e NMT mondat teljes környezetében lefordítani a szavakat. SMT mindössze néhány szót közvetlen összefüggésben előtt és után minden szó.

NMT modellek alapját képezi az API-t, és nem látható a végfelhasználók számára. A csak észrevehető különbségek a következők:
* A továbbfejlesztett fordítási minőségét, különösen a például a kínai, japán és arab nyelvek
* A központ testreszabási funkciója (a Microsoft Translator szöveg API való használatra) alkalmazással való inkompatibilitás

Támogatott beszéd fordítási nyelveket NMT szerint vannak kapcsolva. Ezért a összes beszéd-beszéd átalakítás fordítási NMT használja. 

Beszédfelismerés szöveg fordítási előfordulhat, hogy használja a NMT és SMT attól függően, hogy a két nyelv között. A megadott nyelv NMT támogatja, a teljes fordítási akkor NMT által biztosított. A megadott nyelv NMT által nem támogatott, ha a fordítás egy hibrid NMT és SMT angol, "pivot" között a két nyelv használatával. 

A nézet támogatott nyelvek [Microsoft.com](https://www.microsoft.com/en-us/translator/languages.aspx). 

További információ [NMT működése](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [feliratkozni](translator-speech-how-to-signup.md)

> [!div class="nextstepaction"]
> [Elkezdésére](quickstarts/csharp.md)

## <a name="see-also"></a>Lásd még
- [Kognitív szolgáltatások dokumentációja lap](https://docs.microsoft.com/azure/#pivot=products&panel=cognitive)
- [Kognitív Services termék oldalát](https://azure.microsoft.com/services/cognitive-services/)
- [Megoldás és a díjszabással kapcsolatos](https://www.microsoft.com/en-us/translator/home.aspx) 
