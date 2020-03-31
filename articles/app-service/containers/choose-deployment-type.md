---
title: Linux-tárolók üzembe helyezési lehetőségei
description: Döntsön az egyéni Docker-tároló üzembe helyezése, a többtárolós és a Linuxos App Service beépített alkalmazáskeretrendszer között.
keywords: Azure app szolgáltatás, web app, linux, oss
author: msangapu-msft
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 7c4025e393c196fb98455bac26e94e5f252840b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687515"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Egyéni lemezkép, többtárolós vagy beépített platformkép?

[Az App Service on Linux](app-service-linux-intro.md) három különböző útvonalat kínál az alkalmazás webes közzétételéhez:

- **Egyéni lemezkép-telepítés:**"Dockerize" az alkalmazás egy Docker-rendszerkép, amely tartalmazza az összes fájlt és függőséget egy használatra kész csomagban.
- **Többtárolós üzembe helyezés:**"Dockerize" az alkalmazás több tárolóban egy Docker compose konfigurációs fájl használatával.
- **Alkalmazástelepítés beépített platformlemezképpel**: Beépített platformlemezeink közös webalkalmazás-futtatási és -függőségeket tartalmaznak, például a csomópontot és a PHP-t. Az [Azure App Service telepítési módszereinek](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) bármelyikével telepítheti az alkalmazást a webalkalmazás tárhelyére, majd egy beépített platformlemezképet használhat annak futtatásához.

## <a name="which-method-is-right-for-your-app"></a>Melyik módszer a megfelelő az alkalmazásszámára? 

Az elsődleges figyelembe vemandó tényezők a következők:

- **A Docker rendelkezésre állása a fejlesztési munkafolyamatban:** Az egyéni rendszerkép-fejlesztéshez alapvető ismeretekre van szükség a Docker fejlesztési munkafolyamatáról. Egyéni lemezkép egy webalkalmazás üzembe helyezéséhez az egyéni lemezkép közzétételét igényli egy tárházgazda, például a Docker Hub számára. Ha ismeri a Dockert, és docker-feladatokat adhat hozzá a buildmunkafolyamathoz, vagy ha már docker-lemezképként teszi közzé az alkalmazást, az egyéni lemezkép szinte biztosan a legjobb választás.
- **Többrétegű architektúra:** Több tárolót, például egy webalkalmazás-réteget és egy API-réteget telepíthet a képességek többtárolós használatával történő elkülönítéséhez. 
- **Alkalmazás teljesítmény:** A többtárolós alkalmazás teljesítményének növelése egy gyorsítótár-réteg, például a Redis használatával. Ennek eléréséhez válassza ki a többtárolós tárolót.
- **Egyedi futásidejű követelmények:** A beépített platformrendszerképek a legtöbb webalkalmazás igényeinek megfelelően vannak kialakítva, de a testreszabhatóságuk korlátozott. Előfordulhat, hogy az alkalmazás egyedi függőségekkel vagy más futásidejű követelményekkel rendelkezik, amelyek meghaladják a beépített lemezképek által képes t.
- **Build-követelmények:** A [folyamatos üzembe helyezés,](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)az alkalmazás üzembe helyezése és futtatása az Azure-ban közvetlenül a forráskódból. Nincs szükség külső létrehozási vagy közzétételi folyamatra. Azonban van egy határ a testreszabhatóság és a [kudu](https://github.com/projectkudu/kudu/wiki) üzembe helyezési motor on-val build eszközök rendelkezésre állása. Előfordulhat, hogy az alkalmazás kinövi a Kudu képességeit, ahogy növekszik a függőségek vagy az egyéni buildlogika követelményeinek.
- **Lemezolvasási/-írási követelmények:** Minden webalkalmazás tárolási kötetet kap a webes tartalomhoz. Ez a kötet, az Azure Storage `/home` által támogatott, az alkalmazás fájlrendszerében van csatlakoztatva. A tárolófájlrendszerben lévő fájlokkal ellentétben a tartalomkötetben lévő fájlok az alkalmazás összes léptékpéldányában elérhetők, és a módosítások az alkalmazás újraindításakor is megmaradnak. A tartalomkötet lemezkéste azonban magasabb és változóbb, mint a helyi tárolófájlrendszer késése, és a hozzáférést befolyásolhatják a platformfrissítések, a nem tervezett állásidő és a hálózati kapcsolatproblémái. A tartalomfájlokhoz nagy mértékben írásvédett hozzáférést igénylő alkalmazások számára előnyös lehet az egyéni lemezkép-telepítés, amely a fájlokat a képfájlrendszerben helyezi el a tartalomkötet helyett.
- **Erőforrás-használat létrehozása:** Ha egy alkalmazás a forrásból van telepítve, a Kudu által futtatott központi telepítési parancsfájlok ugyanazokat az App Service-csomag számítási és tárolási erőforrásokat használják, mint a futó alkalmazás. A nagy méretű alkalmazástelepítések a kívántnál több erőforrást vagy időt vehetnek igénybe. Számos üzembe helyezési munkafolyamat nagy lemeztevékenységet generál az alkalmazás tartalomkötetén, amely nincs ilyen tevékenységre optimalizálva. Az egyéni lemezkép egyetlen csomagban biztosítja az alkalmazás összes fájlját és függőségét az Azure-ba, további fájlátvitelek és központi telepítési műveletek nélkül.
- **Gyors iteráció szükségessége:** Egy alkalmazás dockerizing további build lépéseket igényel. Ahhoz, hogy a módosítások érvénybe lépjenek, az új lemezképet minden frissítéssel egy tárházba kell leadnia. Ezek a frissítések ezután lekéri az Azure-környezetben. Ha a beépített tárolók egyike megfelel az alkalmazás igényeinek, a forrásból történő üzembe helyezés gyorsabb fejlesztési munkafolyamatot biztosíthat.

## <a name="next-steps"></a>További lépések

Egyéni tároló:
* [Egyéni tároló futtatása](quickstart-docker-go.md)

Többtartályos:
* [Többtárolós alkalmazás létrehozása](quickstart-multi-container.md)

A következő cikkek a linuxos App Service beépített platformlemezképpel történő ismerkedésével ismerkednek meg:

* [.NET Core](quickstart-dotnetcore.md)
* [Php](quickstart-php.md)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)