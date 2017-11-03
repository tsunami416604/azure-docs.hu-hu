---
title: "Helyi üzembe helyezés Git használatával az Azure App Service szolgáltatásban"
description: "Útmutató az Azure App Service helyi Git-telepítésének engedélyezése."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2016
ms.author: dariagrigoriu
ms.openlocfilehash: ed0239df7bf1e4d37987aaa929d0c67bec595b30
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="local-git-deployment-to-azure-app-service"></a>Helyi üzembe helyezés Git használatával az Azure App Service szolgáltatásban
Ez az oktatóanyag bemutatja, hogyan üzembe az alkalmazást, amely [Azure Web Apps](app-service-web-overview.md) a Git-tárházat a helyi számítógépen. App Service támogatja ezt a módszert a a **helyi Git** a központi telepítési lehetőség a [Azure Portal].  
A jelen cikkben ismertetett Git-parancsok többsége automatikusan megtörténik, egy App Service alkalmazás használatával létrehozásakor a [Azure parancssori felület] leírtak [Itt](app-service-web-get-started-dotnet.md).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Git. Letöltheti a bináris telepítési [Itt](http://www.git-scm.com/downloads).  
* Git alapszintű ismeretét.
* Egy Microsoft Azure-fiók. Ha nincs fiókja, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial), vagy [aktiválhatja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details).

> [!NOTE]
> Ha az Azure App Service első lépései az Azure-fiók regisztrálása előtt szeretné, folytassa a [App Service kipróbálása](https://azure.microsoft.com/try/app-service/), ahol azonnal létrehozhat egy rövid élettartamú alapszintű alkalmazást az App Service-ben. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.  
> 
> 

## <a name="Step1"></a>1. lépés: A helyi tárház létrehozása
A következő feladatokat hozzon létre egy új Git-tárházat.

1. Indítsa el a parancssori eszköz, például a **GitBash** (Windows) vagy **Bash** (Unix rendszerhéj). OS X rendszeren is elérheti a parancssori keresztül a **Terminálszolgáltatások** alkalmazás.
2. Keresse meg azt a könyvtárat, amelyben a tartalom központi telepítéséhez volna található.
3. A következő paranccsal egy új Git-tárház inicializálása:

```bash  
git init
```

## <a name="Step2"></a>2. lépés: A tartalom véglegesítése
App Service számos programozási nyelven készült alkalmazások támogatja. 

1. Ha a tárház már tartalmazza a tartalom kihagyása a pont, és helyezze át az alábbi 2 mutasson. Ha a tárház már tartalmazza a tartalom egyszerűen feltölteni egy statikus .html fájllal az alábbiak szerint: 
   
   * Egy szövegszerkesztő használatával hozzon létre egy új fájlt **index.html** a Git-tárház gyökérkönyvtárában
   * Adja hozzá a következő szöveget a index.html tartalma fájlt, és mentse: *Hello Git!*
2. A parancssorból ellenőrizze, hogy a Git-tárház gyökerében. A következő parancs használatával adja hozzá a fájlok a tárházhoz:

```bash  
git add -A
```
3. Ezt követően véglegesítse a módosításokat a tárházba a következő paranccsal:

```bash  
git commit -m "Hello Azure App Service"
```  

## <a name="Step3"></a>3. lépés: Az App Service alkalmazás tárház engedélyezése
A következő lépésekkel ahhoz, hogy egy App Service-alkalmazás Git-tárházat.

1. Jelentkezzen be az [Azure Portal].
2. Az App Service-alkalmazás paneljén kattintson **beállítások > központi telepítés forrásának**. Kattintson a **forrás választása**, majd kattintson a **helyi Git-tárház**, és kattintson a **OK**.  
   
    ![Helyi Git-tárház](./media/app-service-deploy-local-git/local_git_selection.png)
3. Ha ez az első idő beállítása a tárházat az Azure-ban, a bejelentkezési hitelesítő adatok létrehozása az szeretné. Jelentkezzen be a Azure tárház és leküldéses módosítása a helyi Git-tárház a őket használandó. Az alkalmazás paneljén kattintson **beállítások > üzembe helyezési hitelesítő adatok**, majd konfigurálja a központi telepítés felhasználónevét és jelszavát. Amikor elkészült, kattintson a **mentése**.
   
    ![](./media/app-service-deploy-local-git/deployment_credentials.png)

## <a name="Step4"></a>4. lépés: A projekt telepítése
Az alábbi lépések segítségével közzététele az alkalmazás az App Service helyi Git használatával.

1. Az Azure portálon az alkalmazás paneljén kattintson **beállítások > Tulajdonságok** a a **Git URL-cím**.
   
    ![](./media/app-service-deploy-local-git/git_url.png)
   
    **Git URL-cím** a helyi tárházból telepítendő Távoli hivatkozás. Az URL-cím a következő lépésben fogja használni.
2. Használatával a parancssorból, ellenőrizze, hogy a helyi Git-tárház gyökérkönyvtárában található.
3. Használjon `git remote` hozzáadása a távoli leírásában felsorolt **Git URL-cím** az 1. lépés. A parancs az alábbihoz hasonlóan fog kinézni:
   
        git remote add azure https://<username>@localgitdeployment.scm.azurewebsites.net:443/localgitdeployment.git         
   > [!NOTE]
   > A **távoli** parancs hozzáad egy elnevezett hivatkozást egy távoli tárházba. Ebben a példában a webalkalmazás-tárház "azure" nevű hivatkozást hoz létre.
   > 
   > 
4. Küldje le a tartalmat az új App Service **azure** távoli újonnan létrehozott.

```bash  
git push azure master
```
    You will be prompted for the password you created earlier when you reset your deployment credentials in the Azure Portal. Enter the password (note that Gitbash does not echo asterisks to the console as you type your password). 
5. Térjen vissza az alkalmazást az Azure portálon. A legutóbbi leküldéses a naplóbejegyzést kell megjelennie a **központi telepítések** panelen. 
   
    ![](./media/app-service-deploy-local-git/deployment_history.png)
6. Kattintson a **Tallózás** gomb a tartalom központi telepítésének ellenőrzése az alkalmazás panel tetején. 

## <a name="Step5"></a>Hibaelhárítás
Hibák vagy problémák gyakran fordul elő, ha a Git használatával az Azure App Service alkalmazás közzététele a következők:

- - -
**Jelenség**: nem sikerült hozzáférési [siteURL]: nem sikerült csatlakozni az [scmAddress]

**OK**: Ez a hiba akkor fordulhat elő, ha az alkalmazás nem válik működőképessé.

**Megoldási**: Indítsa el az alkalmazást az Azure-portálon. Git-telepítés nem fog működni, kivéve, ha az alkalmazás futtatása. 

- - -
**Jelenség**: nem sikerült feloldani a gazdagép "állomásnév"

**OK**: Ez a hiba akkor fordulhat elő, ha a megadott létrehozásakor az "azure" távoli címadatokat helytelen volt.

**Megoldási**: használja a `git remote -v` paranccsal listát készíthet az összes távvezérlő, és a kapcsolódó URL-címe. Ellenőrizze, hogy helyes-e az "azure" távoli URL-CÍMÉT. Ha szükséges, távolítsa el és hozza létre újból a távoli, a helyes URL-cím használatával.

- - -
**Jelenség**: nincs közös refs, és nincs megadva; semmi sem történt. Lehet, hogy adja meg például a "master" ág.

**OK**: Ez a hiba akkor fordulhat elő, ha nem ad meg egy ágat git push műveletet, és nem állított be Git által használt push.default értékét.

**Megoldási**: végrehajtani a leküldéses műveletet, adja meg a főágba. Példa:

```bash  
git push azure master
```
- - -
**Jelenség**: src refspec [branchname] nem felel meg egyik sem.

**OK**: Ez a hiba akkor fordulhat elő, ha megkísérli leküldése egy ágat a főadatbázison kívül az "Azure" távoli.

**Megoldási**: végrehajtani a leküldéses műveletet, adja meg a főágba. Példa:

```bash  
git push azure master
```
- - -
**Jelenség**: RPC sikertelen; a eredménye = 22, HTTP-kód = 502-es.

**OK**: Ez a hiba akkor fordulhat elő, ha megkísérli a nagy git-tárház leküldéses HTTPS-KAPCSOLATON keresztül.

**Megoldási**: a helyi gépen a postBuffer nagyobb git konfigurációjának módosítása

```bash  
git config --global http.postBuffer 524288000
```
- - -
**Jelenség**: hiba - távoli tárházba véglegesített módosításokat, de a webalkalmazás nem frissül.

**OK**: Ez a hiba akkor fordulhat elő, ha telepít egy Node.js-alkalmazás, amely meghatározza a további szükséges modulokat package.json fájlt tartalmazó.

**Megoldási**: további üzeneteket tartalmazó "npm hiba!" Ez a hiba a naplózott előtt legyen, és a hiba esetén további környezetet biztosíthat. A következő ismert oka ezt a hibát, és a megfelelő "npm hiba!" üzenet:

* **Nem megfelelően formázott package.json fájl**: npm hiba! Nem lehetett olvasni a függőségek.
* **Natív modul, amely nem rendelkezik Windows bináris terjesztési**:
  
  * npm hiba! \`cmd "/ c" "csomópont-gyp rebuild"\` sikertelen 1
    
      VAGY
  * npm hiba! [modulename@version] preinstall: \`ellenőrizze || gmake\`

## <a name="additional-resources"></a>További források
* [A Git dokumentációja](http://git-scm.com/documentation)
* [Project Kudu dokumentációja](https://github.com/projectkudu/kudu/wiki)
* [Folyamatos telepítés az Azure App Service](app-service-continuous-deployment.md)
* [A PowerShell használata az Azure-ban](/powershell/azure/overview)
* [Az Azure parancssori felület használatával](../cli-install-nodejs.md)

[Azure Developer Center]: http://www.windowsazure.com/en-us/develop/overview/
[Azure Portal]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Azure parancssori felület]: https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/

[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
