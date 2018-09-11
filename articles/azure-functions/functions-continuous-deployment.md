---
title: Az Azure Functions – folyamatos üzembe helyezés |} A Microsoft Docs
description: Az Azure App Service folyamatos üzembe helyezés létesítményekben használatával elvégezhető a közzététel az Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: 7529d20535eedab92d164df5a0435efeda83fca2
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301548"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions – folyamatos üzembe helyezés
Az Azure Functions megkönnyíti az alkalmazás üzembe helyezése a függvény használatával az App Service folyamatos integrációt. Functions integrálható a BitBucket, Dropbox, GitHub és az Azure DevOps. Ez lehetővé teszi egy munkafolyamatot, ahol frissíti a függvénykódot ezek integrált szolgáltatások eseményindító üzembe helyezés az Azure-bA egyikének használatával végzett. Ha most ismerkedik az Azure Functions, kezdje [Azure Functions áttekintése](functions-overview.md).

A folyamatos üzembe helyezés jó megoldás lehet olyan projektek esetén, amelyeknél többszöri és gyakori közreműködői változtatást kell integrálni. Lehetővé teszi a functions-kódhoz a verziókövetés kezelése. Jelenleg a következő központi telepítési források támogatottak:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* Külső tárház (a Git vagy Mercurial)
* [Helyi Git-tárház](../app-service/app-service-deploy-local-git.md)
* [GitHubon](https://github.com)
* [Onedrive vállalati verzió](https://onedrive.live.com/)
* [Az Azure DevOps-szolgáltatásokkal](https://www.visualstudio.com/team-services/)

Központi telepítések függvény alkalmazás alapon vannak konfigurálva. Folyamatos üzembe helyezés engedélyezését követően a függvénykódot a portálon való hozzáférés értéke *csak olvasható*.

## <a name="continuous-deployment-requirements"></a>Folyamatos üzembe helyezési követelményeket

A központi telepítési forrás folyamatos üzembe helyezés előtt rendelkeznie kell a konfigurálva telepítési forrás és a functions-kódhoz. Egy adott függvényt az alkalmazások központi telepítésének egyes függvények él nevű alkönyvtárban, ahol a könyvtár neve a, a függvény.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Az, hogy üzembe helyezése az Azure DevOps, először az Azure-előfizetésében kell kapcsolni az Azure DevOps-szervezet. További információkért lásd: [állítsa be a számlázás az Azure DevOps-szervezet](https://docs.microsoft.com/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs?view=vsts#set-up-billing-via-the-azure-portal).

## <a name="set-up-continuous-deployment"></a>Folyamatos üzembe helyezés beállítása
Ezzel az eljárással meglévő függvényalkalmazással – folyamatos üzembe helyezés konfigurálása. Ezeket a lépéseket egy GitHub-tárházat az integráció bemutatásához, de hasonló lépésekkel Azure DevOps-vagy más központi telepítési szolgáltatások érvényesek.

1. A függvényalkalmazásban, az a [az Azure portal](https://portal.azure.com), kattintson a **platformfunkciók** és **központi telepítési beállítások**. 
   
    ![Folyamatos üzembe helyezés beállítása](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Ezt a a **központi telepítések** panelen kattintson **telepítő**.
 
    ![Folyamatos üzembe helyezés beállítása](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. Az a **központi telepítés forrása** panelen kattintson a **forrás választása**, majd töltse ki a kiválasztott központi telepítés forrása az információkat, és kattintson a **OK**.
   
    ![Válassza ki a központi telepítés forrása](./media/functions-continuous-deployment/choose-deployment-source.png)

Folyamatos üzembe helyezés konfigurálása után a központi telepítés forrása az összes fájlmódosítás lesz másolva a függvényalkalmazást, és akkor indul el, a teljes helyen történő központi telepítés. A helyet a forrásfájlok frissítésekor rendszer újratelepítése.

## <a name="deployment-options"></a>Üzembe helyezési beállítások

Az alábbiakban néhány tipikus telepítési forgatókönyvet:

- [Átmeneti üzembe helyezése](#staging)
- [Helyezze át a meglévő funkciók folyamatos üzembe helyezés](#existing)

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Átmeneti üzembe helyezése

Függvényalkalmazások még nem támogatja az üzembe helyezési pontok. Folyamatos integráció használatával azonban külön átmeneti és éles üzemelő példányok továbbra is kezelheti.

Konfigurálása és használata egy átmeneti üzembe helyezés folyamata általában nézhet ki:

1. Hozzon létre két függvényalkalmazás előfizetésében, egyet az éles kódban, és egy átmeneti. 

2. Ha még nem rendelkezik egy, hozzon létre egy központi telepítés forrása. Ez a példa [GitHubon].

3. A termelési függvényalkalmazás befejeződött az előző lépést **folyamatos üzembe helyezés** és állítsa be az üzembe helyezés ágat a GitHub-tárház főágába.
   
    ![Üzembe helyezés ág kiválasztása](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. Ismételje meg ezt a lépést a átmeneti függvényalkalmazás, de ehelyett válassza ki az átmeneti ág a GitHub-tárban. Ha a központi telepítés forrása nem támogatja az elágaztatást, használjon egy másik mappába.
    
5. Hajtsa végre a frissítéseket a kód a átmeneti ág vagy a mappát, majd győződjön meg arról, hogy ezek a módosítások megjelennek-e az átmeneti üzembe helyezés.

6. Tesztelés után egyesítési módosítja az átmeneti ágból a főágba. A merge elindítja a termelési függvényalkalmazás üzembe helyezés. Ha a központi telepítés forrása nem támogatja az ágak, az átmeneti mappából a fájlokat írja felül a termelési mappában található fájlokat.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Helyezze át a meglévő funkciók folyamatos üzembe helyezés
Ha rendelkezik meglévő funkciók, amelyek a létrehozott és karbantartott a portálon, le kell töltenie a meglévő függvény kódfájlok FTP használatával, vagy a helyi Git-tárház előtt is folyamatos üzembe helyezés beállítása, a fent leírt módon. Ehhez az App Service-beállítások a függvényalkalmazás számára. A fájlok letöltését követően feltöltheti őket a választott folyamatos üzembe helyezési forrást.

> [!NOTE]
> Miután beállította a folyamatos integráció, már nem szerkesztheti a forrásfájlokat, a Functions portálján.

- [Hogyan: üzembe helyezési hitelesítő adatok konfigurálása](#credentials)
- [Hogyan: Töltse le a fájlt FTP használatával](#downftp)
- [Útmutató: a helyi Git-tárház használatával fájlok letöltése](#downgit)

<a name="credentials"></a>
#### <a name="how-to-configure-deployment-credentials"></a>Hogyan: üzembe helyezési hitelesítő adatok konfigurálása
Fájlokat tölthet le a függvényalkalmazás az FTP- vagy a helyi Git-tárház, konfigurálnia kell a hitelesítő adatait, a webhely elérésére. Hitelesítő adatok beállítása függvény alkalmazási szinten. A következő lépések segítségével üzembe helyezési hitelesítő adatok beállítása az Azure Portalon:

1. A függvényalkalmazásban, az a [az Azure portal](https://portal.azure.com), kattintson a **platformfunkciók** és **üzembe helyezési hitelesítő adatok**.
   
    ![Helyi üzembe helyezési hitelesítő adatok beállítása](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. Írja be a felhasználónevet és jelszót, majd kattintson a **mentése**. A függvényalkalmazás eléréséhez az FTP- vagy a beépített Git-tárház mostantól használhatja ezeket a hitelesítő adatokat.

<a name="downftp"></a>
#### <a name="how-to-download-files-using-ftp"></a>Hogyan: Töltse le a fájlt FTP használatával

1. A függvényalkalmazásban, az a [az Azure portal](https://portal.azure.com), kattintson a **platformfunkciók** és **tulajdonságok**, majd másolja a tartozó értékeket. **FTP vagy üzembe helyező felhasználó**, **FTP-állomásnév**, és **FTPS-állomásnév**.  

    **FTP-/ üzembehelyezési felhasználói** , meg kell adni a portálon, valamint az alkalmazás nevének megfelelő környezetet biztosít az FTP-kiszolgáló a név jelenik meg.
   
    ![Az üzembe helyezési adatait](./media/functions-continuous-deployment/get-deployment-credentials.png)

2. Az FTP-ügyféllel, használja a kapcsolati adatokat gyűjtött az alkalmazás és az a funkciók a forrásfájlok letöltéséhez.

<a name="downgit"></a>
#### <a name="how-to-download-files-using-a-local-git-repository"></a>Hogyan: Töltse le a fájlt egy helyi Git-tárház használatával

1. A függvényalkalmazásban, az a [az Azure portal](https://portal.azure.com), kattintson a **platformfunkciók** és **központi telepítési beállítások**. 
   
    ![Folyamatos üzembe helyezés beállítása](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Ezt a a **központi telepítések** panelen kattintson **telepítő**.
 
    ![Folyamatos üzembe helyezés beállítása](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. Az a **központi telepítés forrása** panelen kattintson a **helyi Git-tárház** majd **OK**.

3. A **platformfunkciók**, kattintson a **tulajdonságok** , és jegyezze fel a Git URL-cím értékét. 
   
    ![Folyamatos üzembe helyezés beállítása](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. Klónozza a tárházat a helyi gépen tisztában a Git parancsot a parancssorba vagy a kedvenc Git-eszközt használ. A Git clone parancs így néz ki:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. Adatbeolvasás fájlok a függvényalkalmazásból a klón a helyi számítógépen, az alábbi példában látható módon:
   
        git pull origin master
   
    Ha a rendszer kéri, adja meg a [üzembe helyezési hitelesítő adatok konfigurált](#credentials).  

[GitHubon]: https://github.com/

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Functions – ajánlott eljárások](functions-best-practices.md)
