---
title: Az Azure Blockchain workbench előzetes verziójának üzembe helyezése
description: Az Azure Blockchain Workbench előzetes verziójának telepítése
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 141bb8825e47eb2309f9f551990a2976e8f4e209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943207"
---
# <a name="deploy-azure-blockchain-workbench-preview"></a>Az Azure Blockchain workbench előzetes verziójának üzembe helyezése

Az Azure Blockchain Workbench Preview egy megoldássablon használatával van telepítve az Azure Marketplace-en. A sablon leegyszerűsíti a blokklánc-alkalmazások létrehozásához szükséges összetevők üzembe helyezését. Üzembe helyezés után a Blockchain Workbench hozzáférést biztosít az ügyfélalkalmazásokhoz a felhasználók és a blokklánc-alkalmazások létrehozásához és kezeléséhez.

A Blockchain Workbench összetevőiről az [Azure Blockchain Workbench architektúrában](architecture.md)talál további információt.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="prepare-for-deployment"></a>Felkészülés az üzembe helyezésre

A Blockchain Workbench lehetővé teszi egy blokklánc-főkönyv üzembe helyezését a blokklánc-alapú alkalmazások létrehozásához leggyakrabban használt releváns Azure-szolgáltatásokkal együtt. A Blockchain Workbench üzembe helyezése azt eredményezi, hogy a következő Azure-szolgáltatások kiépítése egy erőforráscsoporton belül az Azure-előfizetésben.

* App Service Csomag (standard)
* Application Insights
* Event Grid
* Azure Key Vault
* Service Bus
* SQL-adatbázis (S0 szabvány) + SQL logikai kiszolgáló
* Azure Storage-fiók (Standard LRS)
* 1-es kapacitású virtuálisgép-méretezési csoport
* Virtuális hálózati erőforráscsoport (terheléselosztóval, hálózati biztonsági csoporttal, nyilvános IP-címmel, virtuális hálózattal)
* Azure Blockchain szolgáltatás. Ha egy korábbi Blockchain Workbench-telepítést használ, fontolja meg az Azure Blockchain Workbench újratelepítését az Azure Blockchain szolgáltatás használatához.

Az alábbiakban egy példa a **myblockchain** erőforráscsoportban létrehozott központi telepítést.

![Példa telepítésre](media/deploy/example-deployment.png)

A Blockchain Workbench költsége az alapul szolgáló Azure-szolgáltatások költségének összesítése. Az Azure-szolgáltatások díjszabási információi a [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)segítségével számítható ki.

## <a name="prerequisites"></a>Előfeltételek

Az Azure Blockchain Workbench használatához Azure AD-konfigurációra és alkalmazásregisztrációkra van szükség. Az Azure [AD-konfigurációk manuálisan,](#azure-ad-configuration) mielőtt üzembe helyezés vagy parancsfájl futtatása utáni üzembe helyezés idot manuálisan. Ha a Blockchain Workbench újratelepítése, tekintse meg az [Azure AD-konfiguráció](#azure-ad-configuration) az Azure AD-konfiguráció ellenőrzése.

> [!IMPORTANT]
> Workbench nem kell telepíteni ugyanabban a bérlőben, mint az Azure AD-alkalmazások regisztrálásához használt. A munkapadot olyan bérlőben kell üzembe helyezni, ahol megfelelő engedélyekkel rendelkezik az erőforrások üzembe helyezéséhez. Az Azure AD-bérlőkről további információt a [Hogyan szerezhet be egy Active Directory-bérlő](../../active-directory/develop/quickstart-create-new-tenant.md) és az alkalmazások [integrálása az Azure Active Directoryval című témakörben.](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)

## <a name="deploy-blockchain-workbench"></a>Blockchain munkaterület telepítése

Miután az előfeltételi lépések befejeződtek, készen áll a Blockchain Workbench üzembe helyezésére. A következő szakaszok ismertetik, hogyan kell telepíteni a keretrendszert.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a fiókját a jobb felső sarokban, és váltson a kívánt Azure AD-bérlőre, ahol az Azure Blockchain Workbench-et szeretné telepíteni.
1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
1. Válassza a **Blockchain** > **Azure Blockchain Workbench (előzetes verzió) lehetőséget.**

    ![Azure Blockchain workbench létrehozása](media/deploy/blockchain-workbench-settings-basic.png)

    | Beállítás | Leírás  |
    |---------|--------------|
    | Erőforrás-előtag | A központi telepítés rövid egyedi azonosítója. Ez az érték az erőforrások elnevezésének alapja. |
    | Virtuális gép felhasználóneve | A felhasználónév az összes virtuális gép (VM) rendszergazdai feladataként használatos. |
    | Hitelesítés típusa | Jelölje be, ha jelszót vagy kulcsot szeretne használni a virtuális gépekhez való csatlakozáshoz. |
    | Jelszó | A jelszó a virtuális gépekhez való csatlakozáshoz használható. |
    | SSH | Használjon RSA nyilvános kulcsot egysoros formátumban, amely **ssh-rsa-val** kezdődik, vagy használja a többsoros PEM formátumot. SSH kulcsokat `ssh-keygen` linuxos és OS X rendszeren, illetve Windows rendszeren a PuTTYGen használatával hozhat létre. Az SSH-kulcsokról további információt az [SSH-kulcsok használata az Azure-ban a Windows rendszerrel című témakörben talál.](../../virtual-machines/linux/ssh-from-windows.md) |
    | Adatbázis és blokklánc-jelszó | Adja meg a központi telepítés részeként létrehozott adatbázishoz való hozzáféréshez használandó jelszót. A jelszónak a következő négy követelmény közül háromnak kell megfelelnie: a hossznak 12 & 72 karakter, 1 kisbetűs, 1 kisbetűs, 1 nagybetűs és 1 speciális\`karakter között kell lennie, amely nem számjel(#), százalék(%), vessző(,), visszaidézet( ), dupla idézőjel(), egyidézőjel('), kötőjel(-) és féloszlop(;) |
    | Telepítési régió | Adja meg, hogy hol szeretné telepíteni a Blockchain Workbench-erőforrásokat. A legjobb elérhetőség érdekében ennek meg kell egyeznie a **Hely** beállítással. |
    | Előfizetés | Adja meg a központi telepítéshez használni kívánt Azure-előfizetést. |
    | Erőforráscsoportok | Hozzon létre egy új erőforráscsoportot az **Új létrehozása** és az egyedi erőforráscsoport nevének megadásával. |
    | Hely | Adja meg a keretrendszer üzembe helyezéséhez kívánt régiót. |

1. Az ALAPbeállítási konfigurációs szakasz befejezéséhez válassza az **OK gombot.**

1. A **Speciális beállítások**területen válassza ki, hogy új blokklánc-hálózatot szeretne-e létrehozni, vagy meglévő proof-of-authority blockchain hálózatot szeretne használni.

    Új **létrehozása:**

    Az *új létrehozása beállítás* üzembe helyez egy Azure Blockchain Service Quorum főkönyvet az alapértelmezett alapszintű termékváltozattal.

    ![Az új blokklánc-hálózat speciális beállításai](media/deploy/advanced-blockchain-settings-new.png)

    | Beállítás | Leírás  |
    |---------|--------------|
    | Az Azure Blockchain szolgáltatás díjszabási szintje | Válassza a Blockchain Workbench hez használt **Alapszintű** vagy **Standard** Azure Blockchain szolgáltatási szint kiválasztása |
    | Az Azure Active Directory beállításai | Válassza a **Hozzáadás később**lehetőséget.</br>Megjegyzés: Ha az [Azure AD előzetes konfigurálását](#azure-ad-configuration) választotta, vagy újratelepíti, válassza a *Hozzáadás most*lehetőséget. |
    | Virtuális gép kiválasztása | Válassza ki az előnyben részesített tárolási teljesítményt és a virtuális gép méretét a blokklánc-hálózathoz. Válasszon egy kisebb virtuális gép méretét, például *a Standard DS1 v2,* ha alacsony szolgáltatási korlátokkal rendelkező előfizetéssel rendelkezik, például az Azure ingyenes csomaggal. |

    Meglévő **használatra:**

    A *meglévő használat* lehetővé teszi az Ethereum Proof-of-Authority (PoA) blokklánc-hálózat megadását. A végpontok a következő követelményekkel rendelkeznek.

   * A végpontnak egy Ethereum Proof-of-Authority (PoA) blokklánc-hálózatnak kell lennie.
   * A végpontnak nyilvánosan elérhetőnek kell lennie a hálózaton keresztül.
   * A PoA blokklánc-hálózatot úgy kell beállítani, hogy a gázára nullára legyen állítva.

     > [!NOTE]
     > A Blockchain Workbench-fiókok nem kerülnek finanszírozásra. Ha pénzre van szükség, a tranzakciók sikertelenek lesznek.

     ![A meglévő blokklánc-hálózat speciális beállításai](media/deploy/advanced-blockchain-settings-existing.png)

     | Beállítás | Leírás  |
     |---------|--------------|
     | Ethereum RPC végpont | Adja meg egy meglévő PoA blokklánc-hálózat RPC-végpontját. A végpont https:// vagy http:// kezdődik és portszámmal végződik. Például: `http<s>://<network-url>:<port>` |
     | Az Azure Active Directory beállításai | Válassza a **Hozzáadás később**lehetőséget.</br>Megjegyzés: Ha az [Azure AD előzetes konfigurálását](#azure-ad-configuration) választotta, vagy újratelepíti, válassza a *Hozzáadás most*lehetőséget. |
     | Virtuális gép kiválasztása | Válassza ki az előnyben részesített tárolási teljesítményt és a virtuális gép méretét a blokklánc-hálózathoz. Válasszon egy kisebb virtuális gép méretét, például *a Standard DS1 v2,* ha alacsony szolgáltatási korlátokkal rendelkező előfizetéssel rendelkezik, például az Azure ingyenes csomaggal. |

1. A Speciális beállítások befejezéséhez válassza az **OK** gombot.

1. Tekintse át az összegzést, és ellenőrizze, hogy a paraméterek pontosak-e.

    ![Összefoglalás](media/deploy/blockchain-workbench-summary.png)

1. Válassza **a Létrehozás** lehetőséget, ha elfogadja a feltételeket, és üzembe helyezi az Azure Blockchain workbench-et.

Az üzembe helyezés akár 90 percet is igénybe vehet. Az Azure Portal segítségével figyelheti a folyamatot. Az újonnan létrehozott erőforráscsoportban válassza **a Központi telepítések > áttekintése** lehetőséget az üzembe helyezett összetevők állapotának megtekintéséhez.

> [!IMPORTANT]
> A telepítés után végre kell végeznie az Active Directory beállításait. Ha a **Hozzá több lehetőséget**választotta, futtatnia kell az Azure [AD konfigurációs parancsfájlját.](#azure-ad-configuration-script)  Ha a **Hozzáadás most**lehetőséget választotta, [konfigurálnia kell a Válasz URL-címét.](#configuring-the-reply-url)

## <a name="blockchain-workbench-web-url"></a>Blockchain Workbench webes URL-címe

Miután a Blockchain Workbench telepítése befejeződött, egy új erőforráscsoport tartalmazza a Blockchain Workbench-erőforrásokat. A Blockchain Workbench-szolgáltatások webes URL-címén keresztül érhetők el. A következő lépések bemutatják, hogyan lehet letölteni a webes URL-t az üzembe helyezett keretrendszer.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A bal oldali navigációs ablakban válassza az **Erőforráscsoportok**lehetőséget.
1. Válassza ki a blockchain workbench telepítésekor megadott erőforráscsoport nevét.
1. A **LISTA** betűszerinti rendezéséhez jelölje ki a TÍPUS oszlopfejlécet.
1. Az **App Service**típusú két erőforrás létezik. Válassza ki az **App Service** típusú erőforrást a "-api" utótag *nélkül.*

    ![Alkalmazásszolgáltatás listája](media/deploy/resource-group-list.png)

1. Az App Service **áttekintése**, másolja az **URL-értéket,** amely a webes URL-t a telepített Blockchain Workbench.

    ![Az alkalmazásszolgáltatás alapjai](media/deploy/app-service.png)

Ha egyéni tartománynevet szeretne társítani a Blockchain Workbench szolgáltatáshoz, olvassa el egy [webalkalmazás egyéni tartománynevének konfigurálása az Azure App Service-ben a Traffic Manager használatával](../../app-service/configure-domain-traffic-manager.md)című témakört.

## <a name="azure-ad-configuration-script"></a>Azure AD konfigurációs parancsfájl

Az Azure AD-t konfigurálni kell a Blockchain Workbench-telepítés befejezéséhez. A konfiguráció t PowerShell-parancsfájl használatával fogja végezni.

1. A böngészőben keresse meg a [Blockchain Workbench webes URL-címét.](#blockchain-workbench-web-url)
1. Az Azure AD cloud shell használatával történő beállításához vonatkozó utasítások jelennek meg. Másolja a parancsot, és indítsa el a Cloud Shellt.

    ![AAD-parancsfájl indítása](media/deploy/launch-aad-script.png)

1. Válassza ki az Azure AD-bérlőt, ahol üzembe helyezte a Blockchain Workbench-et.
1. A Cloud Shell PowerShell környezetben illessze be és futtassa a parancsot.
1. Amikor a rendszer kéri, adja meg az Azure AD-bérlő, amelyet használni szeretne a Blockchain Workbench. Ez lesz a blockchain workbench felhasználóit tartalmazó bérlő.

    > [!IMPORTANT]
    > A hitelesített felhasználó nak engedélyeket kell létrehoznia az Azure AD-alkalmazások regisztrációit, és delegált alkalmazásengedélyeket kell megadnia a bérlőben. Előfordulhat, hogy meg kell kérnie a bérlő egyik rendszergazdáját az Azure AD konfigurációs parancsfájl futtatásához, vagy hozzon létre egy új bérlőt.

    ![Adja meg az Azure AD-bérlőt](media/deploy/choose-tenant.png)

1. A rendszer kéri, hogy egy böngésző használatával hitelesítse magát az Azure AD-bérlő. Nyissa meg a webes URL-címet a böngészőben, írja be a kódot, és hitelesítse magát.

    ![Hitelesítés kóddal](media/deploy/authenticate.png)

1. A parancsfájl több állapotüzenetet ad ki. Sikeres állapotüzenet jelenik **meg,** ha a bérlő sikeresen kilett építve.
1. Nyissa meg a Blockchain Workbench URL-címét. A rendszer arra kéri, hogy járuljon hozzá ahhoz, hogy olvasási engedélyeket adjon a könyvtárnak. Ez lehetővé teszi a Blockchain Workbench webalkalmazás hozzáférést a felhasználók a bérlőben. Ha Ön a bérlői rendszergazda, dönthet úgy, hogy a teljes szervezet beleegyezését adja. Ez a beállítás elfogadja a bérlő összes felhasználójának beleegyezését. Ellenkező esetben minden felhasználó beleegyezését kéri a Blockchain Workbench webalkalmazás első használatához.
1. Válassza az **Elfogadás** a hozzájáruláshoz lehetőséget.

     ![Hozzájárulás a felhasználói profilok olvasásához](media/deploy/graph-permission-consent.png)

1. A jóváhagyás után a Blockchain Workbench webalkalmazás használható.

Befejezte az Azure Blockchain Workbench-telepítést. A telepítés használatának megkezdéséhez a [Következő lépések](#next-steps) témakörben talál.

## <a name="azure-ad-configuration"></a>Az Azure AD konfigurációja

Ha úgy dönt, hogy manuálisan konfigurálja vagy ellenőrzi az Azure AD-beállításokat a telepítés előtt, hajtsa végre az ebben a szakaszban ismertetett összes lépést. Ha szeretné automatikusan konfigurálni az Azure AD-beállításokat, használja [az Azure AD konfigurációs parancsfájlt](#azure-ad-configuration-script) a Blockchain Workbench telepítése után.

### <a name="blockchain-workbench-api-app-registration"></a>Blockchain Workbench API-alkalmazás regisztrációja

A Blockchain Workbench üzembe helyezéséhez egy Azure AD-alkalmazás regisztrációjára van szükség. Az alkalmazás regisztrálásához egy Azure Active Directory (Azure AD) bérlőre van szükség. Használhatja a meglévő bérlő, vagy hozzon létre egy új bérlőt. Ha egy meglévő Azure AD-bérlőt használ, megfelelő engedélyekre van szüksége az alkalmazások regisztrálásához, a Graph API-engedélyek engedélyezéséhez, valamint a vendéghozzáférés engedélyezéséhez egy Azure AD-bérlőn belül. Ha nem rendelkezik a megfelelő engedélyekkel egy meglévő Azure AD-bérlőben, hozzon létre egy új bérlőt.


1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a fiókját a jobb felső sarokban, és váltson a kívánt Azure AD-bérlőre. A bérlő nek az előfizetés i. bérlőjének kell lennie, ahol az Azure Blockchain Workbench telepítve van, és ön rendelkezik az alkalmazások regisztrálásához szükséges engedélyekkel.
1. Válassza ki az **Azure Active Directory** szolgáltatást a bal oldali navigációs panelen. Válassza **az Alkalmazásregisztrációk**új > **regisztrációlehetőséget.**

    ![Alkalmazásregisztráció](media/deploy/app-registration.png)

1. Adjon meg egy **megjelenítendő nevet,** és válassza a **Fiókok lehetőséget ebben a szervezeti címtárban.**

    ![Alkalmazásregisztráció létrehozása](media/deploy/app-registration-create.png)

1. Válassza **a Regisztráció** lehetőséget az Azure AD-alkalmazás regisztrálásához.

### <a name="modify-manifest"></a>Jegyzékfájl módosítása

Ezután módosítania kell a jegyzékfájl az Azure AD-n belüli alkalmazásszerepkörök használatához blockchain workbench-rendszergazdák megadásához.  Az alkalmazásjegyzékekről az [Azure Active Directory alkalmazásjegyzékben](../../active-directory/develop/reference-app-manifest.md)talál további információt.


1. A jegyzékfájlhoz GUID szükséges. Guid-ot a PowerShell paranccsal `[guid]::NewGuid()` vagy `New-GUID` parancsmagmal hozhat létre. Egy másik lehetőség az, hogy egy GUID generátor honlapján.
1. A regisztrált alkalmazáshoz válassza a **Jegyzékfájl** lehetőséget a **Kezelés** szakaszban.
1. Ezután frissítse a **jegyzék alkalmazásszerepek** szakaszát. Cserélje `"appRoles": []` ki a mellékelt JSON.Replace with the provided JSON. Ügyeljen arra, hogy az **azonosító** mező értékét a létrehozott GUID-ra cserélje le. 

    ![Jegyzékfájl szerkesztése](media/deploy/edit-manifest.png)

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    > [!IMPORTANT]
    > A **Rendszergazda** érték szükséges a Blockchain Workbench-rendszergazdák azonosításához.

1. A jegyzékfájlban módosítsa az **Oauth2AllowImplicitFlow** értéket **is true**értékre.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

1. A fájljegyzék-módosítások mentéséhez válassza a **Mentés** gombot.

### <a name="add-graph-api-required-permissions"></a>Graph API hozzáadása szükséges engedélyek hozzáadása

Az API-alkalmazásnak engedélyt kell kérnie a felhasználótól a címtár eléréséhez. Állítsa be a következő szükséges engedélyt az API-alkalmazáshoz:

1. A *Blockchain API-alkalmazás* regisztrációjában válassza az **API-engedélyek et.** Alapértelmezés szerint a Graph API **User.Read** engedély hozzáadódik.
1. A Workbench alkalmazás olvasási hozzáférést igényel a felhasználók alapvető profiladataihoz. A *Konfigurált engedélyek*csoportban válassza **az Engedély hozzáadása**lehetőséget. A **Microsoft API-kban**válassza a **Microsoft Graph**lehetőséget.
1. Mivel a Workbench alkalmazás a hitelesített felhasználói hitelesítő adatokat használja, válassza **a Delegált engedélyek lehetőséget.**
1. A *Felhasználó* kategóriában válassza a **User.ReadBasic.All** engedélyt.

    ![Az Azure AD alkalmazás regisztrációs konfigurációja a Microsoft Graph User.ReadBasic.All delegált engedélyének hozzáadásával](media/deploy/add-graph-user-permission.png)

    Válassza **az Engedélyek hozzáadása**lehetőséget.

1. A *Konfigurált engedélyek*csoportban válassza a **Rendszergazda iaddálása a** tartományhoz lehetőséget, majd válassza az **Igen** lehetőséget az ellenőrzési kérdéshez.

   ![Engedélyek megadása](media/deploy/client-app-grant-permissions.png)

   Az engedély megadása lehetővé teszi, hogy a Blockchain Workbench hozzáférjen a címtárban lévő felhasználókhoz. Az olvasási engedély szükséges a blockchain workbench kereséséhez és hozzáadásához.

### <a name="get-application-id"></a>Alkalmazásazonosító beszereznie

Az alkalmazásazonosító és a bérlői adatok szükségesek a központi telepítéshez. Gyűjtse össze és tárolja az adatokat a telepítés során történő használatra.

1. A regisztrált alkalmazáshoz válassza az **Áttekintés**lehetőséget.
1. Másolja és tárolja az **alkalmazásazonosító** értékét későbbi használatra a telepítés során.

    ![AZ API-alkalmazások tulajdonságai](media/deploy/app-properties.png)

    | A tárolás beállítása  | Használat a telepítésben |
    |------------------|-------------------|
    | Alkalmazás (ügyfél) azonosítója | Az Azure Active Directory beállítása > alkalmazásazonosítója |

### <a name="get-tenant-domain-name"></a>Bérlői tartománynév bekéselése

Gyűjtse össze és tárolja az Active Directory bérlői tartománynevét, ahol az alkalmazások regisztrálva vannak. 

Válassza ki az **Azure Active Directory** szolgáltatást a bal oldali navigációs panelen. Válassza az **Egyéni tartománynevek** elemet. Másolja és tárolja a tartománynevet.

![Tartománynév](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Vendég felhasználói beállítások

Ha az Azure AD-bérlőben vendégfelhasználók vannak, kövesse a további lépéseket a Blockchain Workbench felhasználói hozzárendelésének és kezelésének megfelelő működéséhez.

1. Váltson az Azure AD-bérlőre, és válassza **az Azure Active Directory > felhasználói beállításait > A külső együttműködési beállítások kezelése**lehetőséget.
1. A **Vendég felhasználói engedélyeinek** beállítása **nem.**
    ![Külső együttműködési beállítások](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>A válasz URL-címének konfigurálása

Az Azure Blockchain Workbench üzembe helyezése után konfigurálnia kell az Azure Active Directory (Azure AD) ügyfélalkalmazás **válasz URL-címét** a telepített Blockchain Workbench webes URL-cím.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Ellenőrizze, hogy abban a bérlőben van-e, ahol regisztrálta az Azure AD-ügyfélalkalmazást.
1. Válassza ki az **Azure Active Directory** szolgáltatást a bal oldali navigációs panelen. Válassza **az Alkalmazásregisztrációk lehetőséget.**
1. Válassza ki az Azure AD ügyfélalkalmazást, amelyet az előfeltételi szakaszban regisztrált.
1. Válassza a **Hitelesítés** lehetőséget.
1. Adja meg az Azure Blockchain Workbench által a [Blockchain Workbench webes URL-címszakaszban](#blockchain-workbench-web-url) lekért üzembe helyezés fő webes URL-címét. A válasz URL-címe `https://`előtaggal van ellátva. Például: `https://myblockchain2-7v75.azurewebsites.net`

    ![Hitelesítési válasz URL-címei](media/deploy/configure-reply-url.png)

1. A **Speciális beállítás** szakaszban ellenőrizze az **Access-jogkivonatok** és **azonosítójogtok jelölőnégyzetet.**

    ![Hitelesítés speciális beállításai](media/deploy/authentication-advanced-settings.png)

1. Válassza a **Mentés** gombot az ügyfélregisztráció frissítéséhez.

## <a name="remove-a-deployment"></a>Központi telepítés eltávolítása

Ha már nincs szükség központi telepítésre, eltávolíthatja a központi telepítést a Blockchain Workbench erőforráscsoport törlésével.

1. Az Azure Portalon keresse meg az **Erőforráscsoportot** a bal oldali navigációs ablakban, és válassza ki a törölni kívánt erőforráscsoportot. 
1. Válassza az **Erőforráscsoport törlése** elemet. A törlés ellenőrzése az erőforráscsoport nevének megadásával, majd a **Törlés gombra.**

    ![Erőforráscsoport törlése](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>További lépések

Ebben az útmutató cikkben üzembe helyezte az Azure Blockchain Workbench-et. Ha meg szeretné tudni, hogyan hozhat létre egy blockchain alkalmazást, folytassa a következő útmutató cikkel.

> [!div class="nextstepaction"]
> [Blockchain alkalmazás létrehozása az Azure Blockchain Workbench szolgáltatásban](create-app.md)
