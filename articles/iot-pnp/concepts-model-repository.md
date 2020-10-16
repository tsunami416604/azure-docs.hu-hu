---
title: Az eszköz modell-tárházával kapcsolatos fogalmak ismertetése | Microsoft Docs
description: Megoldás fejlesztőként vagy informatikai szakemberként megismerheti az eszköz modell tárházának alapvető fogalmait.
author: rido-min
ms.author: rmpablos
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4e15ef5256c1552fc8ab7fb9bd84f15bb3433834
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131360"
---
# <a name="device-model-repository"></a>Eszköz modell tárháza

Az eszköz modellje (DMR) lehetővé teszi az eszközök építői számára a IoT Plug and Play eszköz modelljeinek felügyeletét és megosztását. Az eszköz modelljei a [digitális Twins modellezési nyelv (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)használatával meghatározott JSON ld-dokumentumok.

A DMR meghatározza azt a mintát, amely az DTDL-interfészeket az eszköz kettős modell-azonosítója (DTMI) alapján tárolja a mappák struktúrájában. A DMR található felületet úgy keresheti meg, hogy a DTMI egy relatív elérési útra konvertálja. Például a `dtmi:com:example:Thermostat;1` DTMI lefordítja a következőre: `/dtmi/com/example/thermostat-1.json` .

## <a name="public-device-model-repository"></a>Nyilvános eszköz modell tárháza

A Microsoft a következő tulajdonságokkal rendelkező nyilvános DMR üzemeltet:

- Kurátori modellek. A Microsoft felülvizsgálja és jóváhagyja az összes elérhető felületet egy GitHub pull-kérés (PR) érvényesítési munkafolyamattal.
- Módosíthatatlansági.  A közzétételt követően az illesztőfelület nem frissíthető.
- Hyper-Scale. A Microsoft biztosítja a szükséges infrastruktúrát ahhoz, hogy biztonságos, méretezhető végpontot hozzon létre, amely lehetővé teszi az eszközök modelljeinek közzétételét és felhasználását.

## <a name="custom-device-model-repository"></a>Egyéni eszköz modell tárháza

Az egyéni DMR létrehozásához használhatja ugyanazt a DMR-mintát bármilyen adathordozón, például a helyi fájlrendszerben vagy az egyéni HTTP-webkiszolgálókon is. Az egyéni DMR ugyanúgy kérhet le eszközöket, mint a nyilvános DMR, így egyszerűen megváltoztathatja a DMR eléréséhez használt alap URL-címet.

> [!NOTE]
> A Microsoft eszközöket biztosít a nyilvános DMR lévő eszközök modelljeinek ellenőrzéséhez. Ezeket az eszközöket egyéni adattárakban is felhasználhatja.

## <a name="public-models"></a>Nyilvános modellek

A modell adattárában tárolt nyilvános eszköz-modellek mindenki számára elérhetők az alkalmazásaikban való használatra és integrálásra. A nyilvános eszközök modelljei lehetővé teszik, hogy az eszköz-építők és a megoldások fejlesztői számára nyílt öko-rendszer használatával megosszák és újra felhasználhassa a IoT Plug and Play eszköz modelljeit.

Tekintse át a modell [közzététele](#publish-a-model) című szakaszt, amely útmutatást nyújt ahhoz, hogyan tehet közzé egy modellt a modell-adattárban annak érdekében, hogy azok nyilvánosak legyenek.

A felhasználók megkereshetik, megkereshetik és megtekinthetik a hivatalos [GitHub-tárházból](https://github.com/Azure/iot-plugandplay-models)származó nyilvános felületeket.

A mappákban található összes felület `dtmi` a nyilvános végpontról is elérhető. [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Modellek feloldása

Ezen felületek programozott eléréséhez át kell alakítania egy DTMI egy relatív elérési útra, amelynek segítségével lekérdezheti a nyilvános végpontot. A következő mintakód bemutatja, hogyan teheti meg ezt:

DTMI átalakítása abszolút elérési útra a függvényt használjuk a következővel `DtmiToPath` `IsValidDtmi` :

```cs
static string DtmiToPath(string dtmi)
{
    if (!IsValidDtmi(dtmi))
    {
        return null;
    }
    // dtmi:com:example:Thermostat;1 -> dtmi/com/example/thermostat-1.json
    return $"/{dtmi.ToLowerInvariant().Replace(":", "/").Replace(";", "-")}.json";
}

static bool IsValidDtmi(string dtmi)
{
    // Regex defined at https://github.com/Azure/digital-twin-model-identifier#validation-regular-expressions
    Regex rx = new Regex(@"^dtmi:[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?(?::[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?)*;[1-9][0-9]{0,8}$");
    return rx.IsMatch(dtmi);
}
```

Az eredményül kapott elérési úttal és a tárház alap URL-címével a következő felületet tudjuk beszerezni:

```cs
const string _repositoryEndpoint = "https://devicemodels.azure.com";

string dtmiPath = DtmiToPath(dtmi.ToString());
string fullyQualifiedPath = $"{_repositoryEndpoint}{dtmiPath}";
string modelContent = await _httpClient.GetStringAsync(fullyQualifiedPath);
```

## <a name="publish-a-model"></a>Modell közzététele

> [!Important]
> Olyan GitHub-fiókkal kell rendelkeznie, amely lehetővé teszi modellek beküldését a nyilvános DMR.

1. A nyilvános GitHub-tárház elágazása: [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models) .
1. Az elágazó tárház klónozása. Opcionálisan létrehozhat egy új ágat, hogy a módosítások elszigetelve maradjanak a `main` fiókirodában.
1. Adja hozzá az új felületeket a `dtmi` mappához a mappa/fájlnév konvenció használatával. Lásd: [Add-Model](#add-model) eszköz.
1. Ellenőrizze az eszköz modelljeit helyileg a [parancsfájlok használatával a változások érvényesítése](#validate-files) szakaszban.
1. Véglegesítse a módosításokat helyileg, és küldje el az elágazásba.
1. Az elágazásból hozzon létre egy lekéréses kérelmet, amely az ágat célozza meg `main` . Lásd: [probléma létrehozása vagy lekéréses kérelmekre](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) vonatkozó dokumentumok.
1. Tekintse át a [pull-kérelmekre vonatkozó követelményeket](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md).

A lekéréses kérelem elindít egy sorozatú GitHub-műveletet, amely érvényesíti az új elküldött felületeket, és gondoskodik arról, hogy a lekéréses kérelem megfeleljen az összes ellenőrzésnek.

A Microsoft három munkanapon belül minden ellenőrzés után válaszol egy lekéréses kérelemre.

### <a name="add-model"></a>modell hozzáadása

A következő lépések bemutatják, hogyan segíti az add-model.js-szkript az új felület hozzáadását. A parancsfájl futtatásához Node.js szükséges:

1. A parancssorban navigáljon a helyi git-tárházhoz.
1. Az `npm install` parancs futtatása
1. Az `npm run add-model <path-to-file-to-add>` parancs futtatása

Tekintse meg a konzol kimenetét a hibaüzenetek megtekintéséhez.

### <a name="local-validation"></a>Helyi ellenőrzés

A lekéréses kérelem elküldése előtt helyileg is futtathatja az ellenőrzési ellenőrzéseket, hogy segítsen a problémák előzetes diagnosztizálásában.

#### <a name="validate-files"></a>érvényesítés – fájlok

`npm run validate-files <file1.json> <file2.json>` ellenőrzi, hogy a fájl elérési útja megfelel-e a várt mappának és fájlneveknek.

#### <a name="validate-ids"></a>azonosítók ellenőrzése

`npm run validate-ids <file1.json> <file2.json>` ellenőrzi, hogy a dokumentumban meghatározott összes azonosító ugyanazt a gyökeret használja-e, mint a fő azonosító.

#### <a name="validate-deps"></a>ellenőrzés – Deps

`npm run validate-deps <file1.json> <file2.json>` ellenőrzi, hogy az összes függőség elérhető-e a `dtmi` mappában.

#### <a name="validate-models"></a>ellenőrzés – modellek

A [DTDL ellenőrzési minta](https://github.com/Azure-Samples/DTDL-Validator) futtatásával ellenőrizheti az eszközök modelljeit helyileg.

## <a name="next-steps"></a>Következő lépések

A javasolt következő lépés a [IoT Plug and Play architektúra](concepts-architecture.md)áttekintése.
