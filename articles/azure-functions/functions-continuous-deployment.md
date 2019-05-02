---
title: Az Azure Functions – folyamatos üzembe helyezés |} A Microsoft Docs
description: A folyamatos üzembe helyezés eszközökkel, az Azure App Service használatával elvégezhető a közzététel a függvényeket.
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
ms.openlocfilehash: cb3f3ad3bb7b42429654ea4bf9b49f7e230db1da
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943881"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions – folyamatos üzembe helyezés
Az Azure Functions megkönnyíti a folyamatos integráció használatával a függvényalkalmazás üzembe helyezésére. Functions fő kódtárházakhoz, és a központi telepítés forrásának integrálható. Ez az integráció lehetővé teszi, hogy egy munkafolyamatot, ahol frissíti a függvénykódot az Azure-bA ezen szolgáltatások eseményindító üzembe helyezés egyik keresztül történtek. Ha most ismerkedik az Azure Functions, kezdje a [Azure Functions áttekintő](functions-overview.md).

Folyamatos üzembe helyezés egy remek választás hol vagyunk integrálása több és a hozzájárulások gyakran használják. Lehetővé teszi a függvény kódját a verziókövetés kezelése. Az Azure Functions támogatja a következő központi telepítés forrásának:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* Külső tárház (a Git vagy Mercurial)
* [Helyi Git-tárház](../app-service/deploy-local-git.md)
* [GitHubon](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Az Azure DevOps](https://azure.microsoft.com/services/devops/)

Központi telepítések függvény alkalmazás alapon vannak konfigurálva. Folyamatos üzembe helyezés engedélyezését követően a függvénykódot a portálon való hozzáférés értéke *csak olvasható*.

## <a name="requirements-for-continuous-deployment"></a>Folyamatos üzembe helyezési követelményei

Folyamatos üzembe helyezés beállítása előtt rendelkeznie kell a konfigurálva telepítési forrás és a függvény kódját a központi telepítési forrás. A függvény az alkalmazások központi telepítésének egyes funkciója nevű alkönyvtárban, ahol a könyvtár neve a, a függvény.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Az, hogy üzembe helyezése az Azure DevOps, először az Azure-előfizetésében kell kapcsolni az Azure DevOps-szervezet. További információkért lásd: [állítsa be a számlázás az Azure DevOps-szervezet](https://docs.microsoft.com/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs?view=vsts#set-up-billing-via-the-azure-portal).

## <a name="set-up-continuous-deployment"></a>Folyamatos üzembe helyezés beállítása
Ezzel az eljárással meglévő függvényalkalmazással – folyamatos üzembe helyezés konfigurálása. Ezeket a lépéseket egy GitHub-tárházat az integráció bemutatásához, de hasonló lépésekkel Azure DevOps-vagy más központi telepítési szolgáltatások érvényesek.

1. A függvényalkalmazásban, az a [az Azure portal](https://portal.azure.com)válassza **platformfunkciók** > **központi telepítési beállítások**. 
   
    ![Nyissa meg a központi telepítési beállítások szolgáló kiválasztások](./media/functions-continuous-deployment/setup-deployment.png)
 
1. Az a **központi telepítések** panelen válassza ki **telepítő**.
 
    ![Központi telepítések panel](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. Az a **központi telepítés forrása** panelen válassza ki **forrás választása**. A kiválasztott központi telepítés forrása az adatot, és válassza **OK**.
   
    ![A központi telepítés forrásának kiválasztása](./media/functions-continuous-deployment/choose-deployment-source.png)

Miután beállította a folyamatos üzembe helyezés, a központi telepítés forrása az összes fájlmódosítás másolja a függvényalkalmazás, és akkor indul el, a teljes helyen történő központi telepítés. A helyet a forrásfájlok frissítésekor rendszer újratelepítése.

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

Tipikus telepítési forgatókönyvekbe tartoznak, egy átmeneti üzemelő példány létrehozása és áthelyezése a meglévő funkciók folyamatos üzembe helyezés.

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Átmeneti üzembe helyezése

Függvényalkalmazások még nem támogatják az üzembe helyezési pontok. De továbbra is kezelheti a folyamatos integráció használatával külön átmeneti és éles környezetet.

Konfigurálása és használata egy átmeneti üzembe helyezés folyamata általában nézhet ki:

1. Hozzon létre két függvényalkalmazást az előfizetésében: egyet az éles kódban és a egy átmeneti. 

1. Ha még nem rendelkezik egy, hozzon létre egy központi telepítés forrása. Ez a példa [GitHubon].

1. A termelési függvényalkalmazás befejeződött az előző lépést [folyamatos üzembe helyezés](#set-up-continuous-deployment) és állítsa be az üzembe helyezés ágat a GitHub-tárház főágába.
   
    ![A kijelölések telepítési ág kiválasztása](./media/functions-continuous-deployment/choose-deployment-branch.png)

1. Ismételje meg a 3. lépés az előkészítési függvényalkalmazás, de ehelyett válassza ki az átmeneti ág a GitHub-tárban. Ha a központi telepítés forrása nem támogatja az elágaztatást, használjon egy másik mappába.
    
1. Hajtsa végre a frissítéseket a kód a átmeneti ág vagy a mappát, és ellenőrizze, hogy az átmeneti üzembe helyezés tükrözi ezeket a módosításokat.

1. Tesztelés után egyesítési módosítja az átmeneti ágból a főágba. A merge elindítja a termelési függvényalkalmazás üzembe helyezés. Ha a központi telepítés forrása nem támogatja az ágak, az átmeneti mappából a fájlokat írja felül a termelési mappában található fájlokat.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Helyezze át a meglévő funkciók folyamatos üzembe helyezés
Ha rendelkezik meglévő függvények, amelyet létrehozott és karbantartott a portálon, le kell töltenie a függvény kódfájlok FTP használatával, vagy a helyi Git-tárház előtt is folyamatos üzembe helyezés beállítása, a fentebb leírt módon. Ehhez az Azure App Service-beállítások a függvényalkalmazás számára. Miután letöltötte a fájlokat, feltöltheti azokat a választott folyamatos üzembe helyezési forrást.

> [!NOTE]
> Miután beállította a folyamatos integráció, a forrásfájlokat, a Functions portálon már nem szerkesztheti.

<a name="credentials"></a>
#### <a name="configure-deployment-credentials"></a>Üzembe helyezési hitelesítő adatok konfigurálása
Fájl FTP- vagy a helyi Git-tárház használatával tölthet le a függvényalkalmazás, konfigurálnia kell a hitelesítő adatait, a webhely elérésére. Hitelesítő adatok beállítása függvény alkalmazási szinten. A következő lépések segítségével üzembe helyezési hitelesítő adatok beállítása az Azure Portalon:

1. A függvényalkalmazásban, az a [az Azure portal](https://portal.azure.com)válassza **platformfunkciók** > **üzembe helyezési hitelesítő adatok**.
   
1. Adjon meg egy felhasználónevet és jelszót, és válassza ki **mentése**. 

   ![Helyi üzembe helyezési hitelesítő adatok beállítása a beállításokat](./media/functions-continuous-deployment/setup-deployment-credentials.png)

A függvényalkalmazás eléréséhez az FTP- vagy a beépített Git-tárház mostantól használhatja ezeket a hitelesítő adatokat.

<a name="downftp"></a>
#### <a name="download-files-by-using-ftp"></a>Töltse le a fájlokat FTP használatával

1. A függvényalkalmazásban, az a [az Azure portal](https://portal.azure.com)válassza **platformfunkciók** > **tulajdonságok**. Ezután másolja a tartozó értékeket **FTP vagy üzembe helyező felhasználó**, **FTP-állomás neve**, és **FTPS-állomás neve**.  

   **FTP-/ üzembehelyezési felhasználói** , meg kell adni a portálon, valamint az alkalmazás nevének megfelelő környezetet biztosít az FTP-kiszolgáló a név jelenik meg.
   
   ![Az üzembe helyezés információk beállításokat](./media/functions-continuous-deployment/get-deployment-credentials.png)

1. Az FTP-ügyfél azon kapcsolatadatokkal, amelyeket az alkalmazás és az a funkciók a forrásfájlok letöltéséhez összegyűjtött használja.

<a name="downgit"></a>
#### <a name="download-files-by-using-a-local-git-repository"></a>Fájlok letöltése a helyi Git-tárház használatával

1. A függvényalkalmazásban, az a [az Azure portal](https://portal.azure.com)válassza **platformfunkciók** > **központi telepítési beállítások**. 
   
    ![Nyissa meg a központi telepítési beállítások szolgáló kiválasztások](./media/functions-continuous-deployment/setup-deployment.png)
 
1. Ezután a a **központi telepítések** panelen válassza ki **telepítő**.
 
    ![Központi telepítések panel](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. Az a **központi telepítés forrása** panelen válassza ki **helyi Git-tárház** > **OK**.

1. A **platformfunkciók**válassza **tulajdonságok** , és jegyezze fel a Git URL-cím értékét. 
   
    ![A Git URL-cím első szolgáló kiválasztások](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

1. Klónozza a tárházat a helyi gépen a Git-felismerésre képes parancsot a parancssorba vagy a kedvenc Git-eszközt. A Git clone parancs így néz ki:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

1. Adatbeolvasás fájlok a függvényalkalmazásból a klón a helyi számítógépen, az alábbi példában látható módon:
   
        git pull origin master
   
    Ha a rendszer kéri, adja meg a [üzembe helyezési hitelesítő adatok konfigurált](#credentials).  

[GitHubon]: https://github.com/

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Functions – ajánlott eljárások](functions-best-practices.md)
