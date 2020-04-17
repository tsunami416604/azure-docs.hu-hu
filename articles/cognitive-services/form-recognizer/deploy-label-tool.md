---
title: Az Űrlapfelismerő mintacímkéző eszköz telepítése
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogy miként helyezheti üzembe az Űrlapfelismerő mintacímkéző eszközt a felügyelt tanulás elősegítésére.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 7ddb4b2cd465b5e9542d777d33b9bd8cb952becd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531337"
---
# <a name="deploy-the-sample-labeling-tool"></a>A mintacímkézési eszköz üzembe helyezése

Az Űrlapfelismerő mintacímkéző eszköz egy olyan alkalmazás, amely egyszerű felhasználói felületet biztosít, amelyet manuálisan címkézhet fel az űrlapok (dokumentumok) felügyelet iránti tanulás céljából. Ebben a cikkben olyan hivatkozásokat és utasításokat adunk, amelyek megtanítják, hogyan kell:

* [A mintacímkéző eszköz helyi futtatása](#run-the-sample-labeling-tool-locally)
* [A mintacímkéző eszköz üzembe helyezése egy Azure Container Instance (ACI)](#deploy-with-azure-container-instances-aci)
* [A nyílt forráskódú OCR-űrlapcímkéző eszköz használata és közreműködése](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>A mintacímkéző eszköz helyi futtatása

Az adatok címkézésének megkezdésének leggyorsabb módja a mintacímkéző eszköz helyi futtatása. A következő rövid útmutató a Form Recognizer REST API-t és a mintacímkéző eszközt használja egy egyéni modell manuálisan címkézett adatokkal történő betanításához. 

* [Rövid útmutató: Űrlapok címkézése, modell betanítása és űrlap elemzése a mintacímkéző eszközzel](./quickstarts/label-tool.md).

## <a name="deploy-with-azure-container-instances-aci"></a>Üzembe helyezés az Azure Container Instances (ACI) segítségével

Mielőtt elkezdené, fontos megjegyezni, hogy két módon telepítheti a mintacímkéző eszközt egy Azure Container Instance (ACI). Mindkét beállítás az ACI-vel való mintacímkéző eszköz futtatásához használható: 

* [Az Azure Portal használata](#azure-portal)
* [Az Azure CLI-vel](#azure-cli)

### <a name="azure-portal"></a>Azure Portal

Az alábbi lépésekkel új erőforrást hozhat létre az Azure Portalon: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/signin/index/).
2. Válassza az **Erőforrás létrehozása** lehetőséget. 
3. Ezután válassza a **Web App**lehetőséget . 

   > [!div class="mx-imgBorder"]
   > ![Webalkalmazás kiválasztása](./media/quickstarts/formre-create-web-app.png)
   
4. Először győződjön meg arról, hogy az **Alapok** lap ki van jelölve. Meg kell adnia néhány információt: 

   > [!div class="mx-imgBorder"]
   > ![Alapismeretek kiválasztása](./media/quickstarts/formre-select-basics.png)
   * Előfizetés – Meglévő Azure-előfizetés kiválasztása
   * Erőforráscsoport – Újra felhasználhat egy meglévő erőforráscsoportot, vagy létrehozhat egy újat ehhez a projekthez. Új erőforráscsoport létrehozása ajánlott.
   * Név – Adjon nevet a webalkalmazásnak. 
   * Közzététel – **Docker-tároló** kiválasztása
   * Operációs rendszer - Válassza a **Linux ot**
   * Régió – Válasszon egy olyan régiót, amely nek van értelme az Ön számára.
   * Linux-csomag – Válasszon ki egy tarifacsomagot/csomagot az alkalmazásszolgáltatáshoz. 

   > [!div class="mx-imgBorder"]
   > ![A webalkalmazás konfigurálása](./media/quickstarts/formre-select-docker-linux.png)

5. Ezután válassza a **Docker** fület. 

   > [!div class="mx-imgBorder"]
   > ![Válassza a Docker lehetőséget](./media/quickstarts/formre-select-docker.png)

6. Most konfiguráljuk a Docker-tárolót. Minden mező szükséges, kivéve, ha másként van feltüntetve:

   * Beállítások – **Egyetlen tároló** kijelölése
   * Kép forrása – **Privát beállításjegyzék** kiválasztása 
   * Kiszolgáló URL-címe - Állítsa be ezt`https://mcr.microsoft.com`
   * Felhasználónév (nem kötelező) - Felhasználónév létrehozása. 
   * Jelszó (nem kötelező) – Hozzon létre egy biztonságos jelszót, amelyre emlékezni fog.
   * Kép és címke - Állítsa be ezt a`mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
   * Folyamatos üzembe helyezés – Állítsa ezt **be,** ha automatikus frissítéseket szeretne kapni, amikor a fejlesztőcsapat módosítja a mintacímkéző eszközt.
   * Indítási parancs - Állítsa ezt a`./run.sh eula=accept`

   > [!div class="mx-imgBorder"]
   > ![Docker konfigurálása](./media/quickstarts/formre-configure-docker.png)

7. Ennyi az egész. Ezután válassza **a Véleményezés + Létrehozás**lehetőséget, majd a **Létrehozás lehetőséget** a webalkalmazás üzembe helyezéséhez. Ha elkészült, elérheti a webalkalmazást az erőforrás **áttekintése** című témakörben megadott URL-címen.

> [!NOTE]
> A webalkalmazás létrehozásakor engedélyezési/hitelesítési beállításokat is konfigurálhat. Ez nem szükséges az első lépésekhez. 

### <a name="azure-cli"></a>Azure CLI

Az Azure Portal használatával alternatívájaként létrehozhat egy erőforrást az Azure CLI használatával. Mielőtt folytatna, telepítenie kell az [Azure CLI-t.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Ezt a lépést kihagyhatja, ha már dolgozik az Azure CLI-vel. 

Van néhány dolog, amit tudnia kell erről a parancsról:

* `DNS_NAME_LABEL=aci-demo-$RANDOM`véletlenszerű DNS-nevet hoz létre. 
* Ez a minta feltételezi, hogy rendelkezik egy erőforráscsoporttal, amely segítségével hozzon létre egy erőforrást. Cserélje `<resource_group_name>` le az előfizetéshez társított érvényes erőforráscsoportra. 
* Meg kell adnia, hogy hol szeretné létrehozni az erőforrást. Cserélje `<region name>` le a kívánt régiót a webalkalmazáshoz. 
* Ez a parancs automatikusan elfogadja a eula-t.

Az Azure CLI-ből futtassa ezt a parancsot egy webalkalmazás-erőforrás létrehozásához a mintacímkéző eszközhöz: 

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resorunce_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8
  --command-line "./run.sh eula=accept"
```

### <a name="connect-to-azure-ad-for-authorization"></a>Csatlakozás az Azure AD-hez engedélyezésre

Javasoljuk, hogy csatlakoztassa a webalkalmazást az Azure Active Directoryhoz. Ez biztosítja, hogy csak az érvényes hitelesítő adatokkal rendelkező felhasználók jelentkezhessenek be és használhassák a webalkalmazást. Kövesse az [App Service-alkalmazás konfigurálása az](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) Azure Active Directoryhoz való csatlakozáshoz című, az utasításokat.

## <a name="open-source-on-github"></a>Nyílt forráskódú a GitHubon

Az OCR-űrlapcímkéző eszköz nyílt forráskódú projektként is elérhető a GitHubon. Az eszköz egy webes alkalmazás segítségével készült React + Redux, és meg van írva TypeScript. További információért vagy közreműködésért olvassa el [az OCR-űrlapcímkéző eszköz .](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)

## <a name="next-steps"></a>További lépések

A [Train with labels](./quickstarts/label-tool.md) rövid útmutató segítségével megtudhatja, hogyan használhatja az eszközt a betanítási adatok manuális címkézésére és felügyelt tanulás végrehajtására.
