---
title: Címke és a verzió az Azure Container Registry rendszerképek
description: Ajánlott eljárások a címkézés és a versioning Docker tárolórendszerképek
services: container-registry
author: stevelasker
ms.service: container-registry
ms.topic: article
ms.date: 07/10/2019
ms.author: steve.lasker
ms.openlocfilehash: bd00fd4f8dd247c766eb34849ecf9de603c5171b
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800392"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Tárolórendszerképek címkézése és verziókezelését javaslatok

Üzembe helyezése tárolórendszerképek leküldése egy tároló-beállításjegyzéket, és ezután telepíti őket, amikor szükség van egy stratégia képcímkézés és verziókezelését. Ez a cikk ismerteti a két megközelítést, és ahol minden megfelelő tároló életciklusa során:

* **A címkék stabil** -jelzi a fő vagy alverziót például újból felhasználja, például címkék *mycontainerimage:1.0*.
* **Egyedi címkék** – az egyes lemezképek például leküldi a beállításjegyzékbe, egy másik címkét *mycontainerimage:abc123*.

## <a name="stable-tags"></a>Stabil címkék

**Az ajánlás**: Stabil címkék használatával karbantartása **lemezképek kiinduló** esetében a tároló épít. Kerülje a stabil címkékkel rendelkező központi telepítések, hiszen ezen címkék továbbra is kaphat frissítéseket, és éles környezetben lévő inkonzisztenciák megjelentetni.

*A címkék stabil* jelenti azt, hogy a fejlesztők vagy a buildelési rendszert, továbbra is egy adott címkével, és továbbra is frissítéseket lekéréses. Stabil nem jelenti azt, a tartalma szüneteltetve legyenek. Stabil, azt jelenti, a kép stabil a szándéka, hogy verziót kell lennie. Maradjon "stabil", előfordulhat, hogy szolgálja ki biztonsági javítások vagy framework-frissítések alkalmazásához.

### <a name="example"></a>Példa

A keretrendszer csapat mobilplatform 1.0-s verziója. Tudni fogják, hogy azok fog tegye elérhetővé a frissítéseket, köztük a kisebb. Egy adott fő- és alverzió verzió támogatja az állandó címkéket, két készletnyi stabil címkékkel rendelkeznek.

* `:1` – a főverzió stabil címkét. `1` a "legújabb" vagy "legutóbbi" 1.* verziót jelöli.
* `:1.0`– 1.0-s verziója lehetővé teszi a frissítések 1.0 kötést létrehozni, és nem állítható előre 1.1 akkor szabadul fel, amikor egy fejlesztő stabil címkét.

A csapata is használ a `:latest` címke, amely a legújabb stabil címke, függetlenül attól, hogy milyen a jelenlegi fő verzió mutat.

Amikor alaplemezkép frissítések érhetők el, vagy bármilyen típusú, a keretrendszert, a képeket a stabil címkékkel kiadása karbantartási frissítve lett, hogy a legújabb kivonatoló, amely a legújabb stabil kiadás, azt a verziót jelöli.

Ebben az esetben a fő- és alverzió címkék vannak folyamatosan szolgálja ki. Alaplemezkép forgatókönyv lehetővé teszi a lemezkép tulajdonosa szervizelt rendszerképeket biztosít.

## <a name="unique-tags"></a>Egyedi címkék

**Az ajánlás**: Az egyedi címkékkel **központi telepítések**, különösen olyan környezetben, több csomóponton méretezése sikerült. Valószínűleg érdemes szándékos összetevők megegyező verziójának telepítéséhez. Ha a tároló újraindul, vagy az orchestrator elvégzi a horizontális felskálázást további példányok, a gazdagépek nem fog véletlenül lekéréses egy újabb verzióra, a többi csomópont konzisztens.

Egyedi címkézés egyszerűen azt jelenti, hogy minden rendszerképet leküldte a tárolójegyzékbe egyedi kódot. A címkék nem újra felhasználhatók. Van néhány minták egyedi címkéket, beleértve a létrehozásához követheti:

* **Dátum-idő bélyeg** – Ez a megközelítés akkor viszonylag egyszerű, mivel egyértelműen mutatja, ha a rendszerkép lett létrehozva. De hogyan kapcsolható vissza a buildelési rendszer? Keresse meg a build egy időben végrehajtott van? Milyen időzónában tartózkodik? Korlátokon vannak belül minden build rendszerét az UTC Időzóna?
* **A Git commit** – a módszer jól használható, amíg alaplemezkép frissítések támogató megkezdése. Ha egy rendszerkép alapszintű frissítésének történik, az összeállítási rendszer elindít az azonos Git véglegesítéshez, mint a korábbi build. Azonban az alaprendszerképet rendelkezik új tartalmat. Általánosságban véve a Git commit biztosít egy *félig*-címke stabil.
* **Jegyzékfájl kivonatoló** – minden tárolórendszerképet fel leküldéssel továbbíthat egy tárolóregisztrációs adatbázisba társítva egy egyedi SHA-256 kivonatoló vagy kivonatoló által azonosított-jegyzék,. Egyedi, míg a kivonatoló hosszú, nehezen olvasható, de a build-környezete predikátumban.
* **Azonosító létrehozása** – Ez a beállítás ajánlott lehet, mivel valószínűleg növekményes, és lehetővé teszi az összetevők és a naplók keresése a meghatározott build vissza a korrelációját. Azonban például egy jegyzékfájl kivonatoló lehet nehezen olvasható egy emberi.

  Ha a szervezete több buildelési rendszert, a címke az összeállítási rendszer nevű előtag ezzel a beállítással egy variációja: `<build-system>-<build-id>`. Például sikerült különbséget tenni a buildeket, az API-csapat Jenkins buildelési rendszert, és a webes csapata Azure folyamatokat hozhat létre a rendszer.

## <a name="next-steps"></a>További lépések

A jelen cikk fogalmait részletes tárgyalását lásd: a következő blogbejegyzésben: [Docker címkézés: Ajánlott eljárások a docker-rendszerképek címkézése és verziókezelését](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/).

Annak érdekében, teljesítménnyel és költséghatékonysággal használhatja az Azure container registry, lásd: [ajánlott eljárások az Azure Container Registry](container-registry-best-practices.md).

<!-- IMAGES -->


<!-- LINKS - Internal -->

