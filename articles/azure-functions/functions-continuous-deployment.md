---
title: "Folyamatos üzembe helyezés az Azure Functions |} Microsoft Docs"
description: "Folyamatos üzembe helyezés létesítményekben az Azure App Service segítségével az Azure Functions közzététele."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: 35a0b0faa61cf4b42ba1d8696c85f5724ff73f23
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions – folyamatos üzembe helyezés
Az Azure Functions megkönnyíti az App Service folyamatos integrációt használó függvény alkalmazás telepítése. Funkciók integrálható a Bitbucketből, a dropbox-ba, a GitHub és a Visual Studio Team Services (VSTS). Ez lehetővé teszi egy munkafolyamatot, ha frissíti a funkciókódot ezek integrált szolgáltatások eseményindító telepítése az Azure használatával végrehajtott. Ha most ismerkedik az Azure Functions, kezdje [Azure Functions áttekintése](functions-overview.md).

A folyamatos üzembe helyezés jó megoldás lehet olyan projektek esetén, amelyeknél többszöri és gyakori közreműködői változtatást kell integrálni. Lehetővé teszi a funkciók kódja verziókezelő karbantartása. A következő központi telepítési források jelenleg támogatottak:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox-bA](https://www.dropbox.com/)
* Külső tárház (Git vagy Mercurial)
* [Helyi Git-tárház](../app-service/app-service-deploy-local-git.md)
* [GitHub](https://github.com)
* [Onedrive vállalati verzió](https://onedrive.live.com/)
* [Visual Studio Team Services](https://www.visualstudio.com/team-services/)

Központi telepítések függvény alkalmazás szinten vannak konfigurálva. Folyamatos üzembe helyezés engedélyezése után a portálon funkciókódot elérésére értéke *írásvédett*.

## <a name="continuous-deployment-requirements"></a>Folyamatos üzembe helyezés feltételeit

A központi telepítés forrásának beállítása a folyamatos üzembe helyezés előtt a konfigurálva telepítési forrás és a funkciók kódot kell rendelkeznie. Egy adott funkció alkalmazások központi telepítésének egyes függvény él, egy elnevezett alkönyvtárra, ahol a könyvtárnév pedig a függvény nevét.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="set-up-continuous-deployment"></a>Folyamatos üzembe helyezés beállítása
Ezzel az eljárással konfigurálhatja folyamatos üzembe egy meglévő függvény alkalmazást. Ezeket a lépéseket egy GitHub-tárházban az integráció bemutatásához, de hasonló lépésekkel a Visual Studio Team Services vagy egyéb telepítési érvényesek.

1. Az alkalmazás a függvény a [Azure-portálon](https://portal.azure.com), kattintson a **Platform funkciói** és **központi telepítési beállítások**. 
   
    ![Folyamatos üzembe helyezés beállítása](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Ezt a a **központi telepítések** panelen kattintson **telepítő**.
 
    ![Folyamatos üzembe helyezés beállítása](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. Az a **központi telepítés forrásának** panelen kattintson a **forrás választása**, majd adja meg a kiválasztott központi telepítés forrásának adatait, és kattintson a **OK**.
   
    ![Válassza ki a központi telepítés forrása](./media/functions-continuous-deployment/choose-deployment-source.png)

Folyamatos üzembe helyezés beállítása után a központi telepítés forrásának összes fájl módosításának kerülnek a függvény alkalmazást, és akkor váltódik ki, a teljes helyen történő központi telepítés. A hely frissítésekor a fájljai van újratelepítése.

## <a name="deployment-options"></a>Üzembe helyezési lehetőségek

Az alábbiakban néhány tipikus telepítési forgatókönyv:

- [Átmeneti központi telepítés](#staging)
- [Helyezze át a meglévő funkciók a folyamatos üzembe helyezés](#existing)

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Átmeneti központi telepítés

Alkalmazások funkció még nem támogatja a üzembe helyezési pontok. Folyamatos integrációt használatával azonban külön átmeneti és üzemi központi telepítések továbbra is kezelheti.

A folyamat konfigurálását és használatát az átmeneti telepítést általában formátuma a következő:

1. Az előfizetés, az éles kódot és egy az átmeneti két függvény-alkalmazásai létrehozására. 

2. Hozzon létre olyan telepítési forrás, ha még nem rendelkezik. Ez a példa [GitHub].

3. Az éles függvény alkalmazás teljes a fenti lépéseket **folyamatos üzembe helyezés beállítása** , és a központi telepítés ágat a GitHub-tárház főágába.
   
    ![Válassza ki a központi telepítés ág](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. Ismételje meg ezt a lépést az átmeneti tárolási függvény alkalmazás, de ehelyett válassza ki a átmeneti ágat a GitHub-tárház. Ha a központi telepítési forrás nem támogatja a ugorhat, használjon egy másik mappába.
    
5. Hogy a kód a átmeneti fiókirodai vagy mappát, majd győződjön meg arról, hogy ezek a módosítások megjelennek-e a átmeneti központi telepítés.

6. Tesztelés után lévő módosítások átmeneti ágat a főágba történő. A merge elindítja a központi telepítést, hogy az éles függvény alkalmazást. Ha a központi telepítési forrás nem támogatja az ágakkal rendelkező, a termelési mappában lévő fájlok felülírása az átmeneti mappából a fájlokat.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Helyezze át a meglévő funkciók a folyamatos üzembe helyezés
Ha rendelkezik meglévő függvények, amelyek létrehozása és karbantartása a portálon, le kell töltenie a meglévő függvény kódfájlok FTP használatával, vagy a helyi Git-tárház előtt állíthat be a folyamatos üzembe helyezés fent leírt módon. Ehhez az App Service-beállítások az funkciót alkalmazásához. A fájlok letöltését követően töltse fel őket a kiválasztott folyamatos üzembe helyezés forráshoz.

> [!NOTE]
> Miután konfigurálta a folyamatos integrációt, már nem lesz a forrásfájlok a Functions portálon szerkesztheti.

- [Hogyan: üzembe helyezési hitelesítő adatok konfigurálása](#credentials)
- [Útmutató: FTP használata fájlok letöltése](#downftp)
- [Útmutató: a helyi Git-tárház fájlok letöltése](#downgit)

<a name="credentials"></a>
#### <a name="how-to-configure-deployment-credentials"></a>Hogyan: üzembe helyezési hitelesítő adatok konfigurálása
A függvény alkalmazás FTP-vagy helyi Git-tárház letöltheti a fájlokat, konfigurálnia kell a hitelesítő adatok a webhelyhez való hozzáféréshez. A függvény alkalmazási szintű hitelesítő adatok beállítása. Az alábbi lépések segítségével üzembe helyezési hitelesítő adatok beállítása az Azure-portálon:

1. Az alkalmazás a függvény a [Azure-portálon](https://portal.azure.com), kattintson a **Platform funkciói** és **üzembe helyezési hitelesítő adatok**.
   
    ![Helyi telepítési hitelesítő adatok beállítása](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. Írja be a felhasználónevet és jelszót, majd kattintson az **mentése**. A függvény app hozzáférni az FTP- vagy a beépített Git-tárház használhatja ezeket a hitelesítő adatokat.

<a name="downftp"></a>
#### <a name="how-to-download-files-using-ftp"></a>Útmutató: FTP használata fájlok letöltése

1. Az alkalmazás a függvény a [Azure-portálon](https://portal.azure.com), kattintson a **Platform funkciói** és **tulajdonságok**, majd másolja a **FTP vagy üzembe helyező felhasználó**, **FTP állomásnév**, és **FTPS állomásnév**.  

    **FTP-vagy üzembe helyező felhasználó** meg kell adni, megjelenik a portálon, beleértve az alkalmazásnév, megfelelő környezetet biztosít az FTP-kiszolgáló.
   
    ![A központi telepítési információk](./media/functions-continuous-deployment/get-deployment-credentials.png)

2. Az FTP-ügyfél, használja a kapcsolati adatok összegyűjtött való csatlakozáshoz az alkalmazáshoz és a funkciók számára a forrásfájlok letöltéséhez.

<a name="downgit"></a>
#### <a name="how-to-download-files-using-a-local-git-repository"></a>Hogyan: Töltse le a fájlokat a helyi Git-tárház

1. Az alkalmazás a függvény a [Azure-portálon](https://portal.azure.com), kattintson a **Platform funkciói** és **központi telepítési beállítások**. 
   
    ![Folyamatos üzembe helyezés beállítása](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Ezt a a **központi telepítések** panelen kattintson **telepítő**.
 
    ![Folyamatos üzembe helyezés beállítása](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. Az a **központi telepítés forrásának** panelen kattintson a **helyi Git-tárház** , majd **OK**.

3. A **Platform funkciói**, kattintson a **tulajdonságok** és Git URL-cím értékét. 
   
    ![Folyamatos üzembe helyezés beállítása](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. Klónozza a tárházat a helyi számítógépen a Git-kompatibilis parancssor vagy a kedvenc Git eszköz használatával. A Git-klón parancs így néz ki:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. Lehívási fájlok a függvény alkalmazás a klón a helyi számítógépen, az alábbi példában látható módon:
   
        git pull origin master
   
    Ha szükséges, adja meg a [üzembe helyezési hitelesítő adatok konfigurált](#credentials).  

[GitHub]: https://github.com/

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Functions – ajánlott eljárások](functions-best-practices.md)
