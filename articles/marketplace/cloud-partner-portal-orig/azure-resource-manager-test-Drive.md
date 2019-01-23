---
title: Az Azure Resource Manager kipróbálása |} A Microsoft Docs
description: Hozhat létre a Marketplace-en próbálja ki az Azure Resource Manager használatával
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick .Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: b7cbd69a4551605b71930a23f837b467177e3cc3
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54451357"
---
<a name="azure-resource-manager-test-drive"></a>Az Azure Resource Manager kipróbálása
=================================

Ez a cikk a kiadók, akik az ajánlatot az Azure piactéren, illetve akik az appsource-on rendelkezik, de létre szeretné hozni a Test Drive csak Azure-erőforrásokkal való szól.

Egy Azure Resource Manager-(Azure Resource Manager) sablont az Azure-erőforrások, hogy tervezzen ajánlott, amelyek a megoldás kódolt tárolója. Ha még nem ismeri a milyen egy Resource Manager-sablon, olvassa a [ARM-sablonok ismertetése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) és [ARM-sablonok készítése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) , hogy már tudja, hogyan hozhat létre, és tesztelje a saját sablonjait.

Test Drive funkciója, hogy a megadott Resource Manager-sablon vesz igénybe, és lehetővé teszi egy erőforrás-csoportba a Resource Manager-sablon a szükséges összes erőforrást üzembe helyezése.

Ha hozhat létre egy Azure Resource Manager Test Drive választja, a követelmények a következők meg:

- Összeállítását, tesztelését és majd töltse fel a teszt meghajtó Resource Manager-sablont.
- Az összes szükséges metaadatokat és a Test Drive engedélyezéséhez beállításainak konfigurálása.
- Tegye közzé újra az ajánlatot a Test Drive engedélyezve van.

<a name="how-to-build-an-azure-resource-manager-test-drive"></a>Hogyan hozhat létre egy Azure Resource Manager kipróbálása
------------------------------

A legfontosabb eleme egy Azure Resource Manager Test Drive létrehozásával kapcsolatos, hogy megadhatja, milyen eset az ügyfelek tapasztalhat. Ön egy tűzfal terméket, és szeretné bemutató arról, hogy kezeli a parancsfájl-injektálási támadások? Azok a tárolási termék, és szeretné bemutató milyen gyors és egyszerű a megoldás akkor tömöríti a fájlokat?

Győződjön meg arról, töltött idő kiértékelése, Mik azok a legjobb módszerei mutatni, a termék elegendő mennyiségű óvadék. Kifejezetten a szükséges erőforrások köré kell, mivel lehetővé teszi a Resource Manager-sablon megfelelő könnyebbé mikroszolgáltatásokra.

A tűzfal példát folytatva, az architektúra lehet, hogy van szüksége egy nyilvános IP a szolgáltatás és egy másik nyilvános IP URL-címe a webhelyet, amelyet a tűzfal védi. Minden egyes IP üzembe helyezett virtuális gépen, és együtt egy hálózati biztonsági csoport és a hálózati adapter csatlakozik.

Miután a kívánt csomagot, az erőforrások rendelkezik lett tervezve, most származik az írási és a Test Drive Resource Manager-sablon létrehozása.

<a name="writing-test-drive-resource-manager-templates"></a>Írás a Test Drive Resource Manager-sablonok
--------------------------------

Egy teljesen automatizált módban, és miatt, amely futtatja a test Drive központi telepítések, a Test Drive-sablonok az alábbiakban néhány korlátozás.

### <a name="parameters"></a>Paraméterek

A legtöbb sablonok paraméterek készletével rendelkeznek. Paraméterek és így tovább definiálása erőforrásnevek, erőforrások méretek (például a storage-fiókok vagy virtuálisgép-méretek esetében), felhasználói neveket és jelszavakat, DNS-neveket. Megoldások az Azure portal használatával történő telepítésekor, manuálisan is feltölti ezeket a paramétereket, válasszon az elérhető DNS-nevét vagy a tárfiókok nevének, és így tovább.

![Az Azure Resource Manager-paramétereinek listáját](./media/azure-resource-manager-test-drive/param1.png)

Azonban Test Drive módban működik egy teljesen automatikus, emberi beavatkozás nélkül, csak korlátozott számú paraméter kategóriák támogatja. Ha a Test Drive Resource Manager-sablon egyik paraméterének nem tartozik a támogatott kategóriák közül, meg kell **változó vagy állandó értéket cserélje le ezt a paramétert.**

Használhat bármilyen érvényes nevet a paramétereket, Test Drive paraméter kategória felismeri a metaadat-típusú érték. Ön **kell adnia minden sablonparaméterhez metaadattípus**, ellenkező esetben a sablon nem felel meg érvényesítési:

    "parameters": {
      ...
      "username": {
        "type": "string",
        "metadata": {
          "type": "username"
        }
      },
      ...
    }

Emellett az is fontos megjegyezni, hogy **minden paraméter megadása nem kötelező**, ha nem így\'t szeretné használni, megosztához\'t kell.

### <a name="accepted-parameter-metadata-types"></a>Elfogadott paramétertípusok metaadatok

| Metaadat típusa   | Paraméter típusa  | Leírás     | Mintaérték    |
|---|---|---|---|---|
| **BaseUri**     | sztring          | Alap URI-t, a központi telepítési csomag| [https://\<\..\>.blob.core.windows.net/\<\..\>](#) |
| **felhasználónév**    | sztring          | Új véletlenszerű felhasználónevet.| admin68876      |
| **jelszó**    | a biztonságos karakterláncot    | Új véletlenszerű jelszó | LP! ACS\^2kh     |
| **A munkamenet-azonosító**   | sztring          | Egyedi Azonosítóját (GUID) Test Drive-munkamenet    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="username"></a>felhasználónév

Test Drive inicializálja ezt a paramétert egy **alap Uri** a központi telepítési csomag, így minden fájlhoz, a csomagba foglalt Uri létrehozásához is használhatja ezt a paramétert.

    "parameters": {
      ...
      "baseuri": {
        "type": "string",
        "metadata": {
          "type": "baseuri",
          "description": "Base Uri of the deployment package."
        }
      },
      ...
    }

A sablon belül a paraméter használatával hozhat létre egy URI-t minden fájlhoz, a Test Drive központi telepítési csomag. Az alábbi példa bemutatja, hogyan hozhat létre a társított sablon Uri:

    "templateLink": {
      "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
      "contentVersion": "1.0.0.0"
    }

#### <a name="username"></a>felhasználónév

Test Drive inicializálja ezt a paramétert, amely új véletlenszerű felhasználónévvel:

    "parameters": {
      ...
      "username": {
        "type": "string",
        "metadata": {
          "type": "username",
          "description": "Solution admin name."
        }
      },
      ...
    }

Mintaérték:

    admin68876

A megoldás véletlenszerű vagy állandó felhasználónevek is használhatja.

#### <a name="password"></a>jelszó

Test Drive inicializálja a paraméternél adja meg az új véletlenszerű jelszó:

    "parameters": {
      ...
      "password": {
        "type": "securestring",
        "metadata": {
          "type": "password",
          "description": "Solution admin password."
        }
      },
      ...
    }

Mintaérték:

    Lp!ACS^2kh

A megoldás állandó vagy véletlenszerű jelszavakat is használhatja.

#### <a name="session-id"></a>A munkamenet-azonosító

Próbálja ki ezt a paramétert inicializálása jelölő Test Drive munkamenet-azonosító egy egyedi GUID:

    "parameters": {
      ...
      "sessionid": {
        "type": "string",
        "metadata": {
          "type": "sessionid",
          "description": "Unique Test Drive session id."
        }
      },
      ...
    }

Mintaérték:

    b8c8693e-5673-449c-badd-257a405a6dee

Használhatja ezt a paramétert a Test Drive munkamenet egyedi azonosításához, ha szükséges.

### <a name="unique-names"></a>Egyedi nevek

Egyes Azure-erőforrások, például a storage-fiókok vagy DNS-nevek, globálisan egyedi nevek van szükség.

Ez azt jelenti, hogy minden alkalommal, amikor a Test Drive üzembe helyezte a Resource Manager-sablon, létrehoz egy **egy egyedi nevet az új erőforráscsoport** összes annak\' erőforrásokat. Ezért azt kell használnia a [uniquestring](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) függvény és a változók nevében erőforráscsoporton azonosítókat létrehozni véletlenszerű egyedi értékeket:

      "variables": {
      ...
      "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
      "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
      ...
    }

Győződjön meg arról, hogy a paraméter vagy változó karakterlánc összefűzésére (\'contosovm\') és egy egyedi karakterlánc-kimenet (\'resourceGroup () .id\'), mert ez garantálja, hogy az egyedi-e, és a megbízhatóság az egyes.

Például a legtöbb erőforrás neve nem kezdődhet számjeggyel, de egyedi karakterlánc függvény visszaadhat egy karakterláncot, amely egy számjegyet kezdődik. Tehát ha nyers egyedi karakterlánc kimeneti használja, az üzembe helyezések meghiúsulnak. 

Erőforrás elnevezési szabályokat és korlátozásokat a további információt talál [Ez a cikk](https://docs.microsoft.com/azure/guidance/guidance-naming-conventions).

### <a name="deployment-location"></a>Üzembe helyezés helye

Elérhetővé teheti, Test Drive különböző Azure-régióban. A cél, hogy lehetővé teszi, hogy válassza ki a legközelebbi régió, a beast felhasználói élményt biztosít.

Test Drive létrehoz egy példányt a labor létrehozása, ha mindig létrehoz egy erőforráscsoportot a felhasználó által a régiót válassza, és végrehajtja a központi telepítési sablont a csoport a környezetben. Így a sablon ki kell választania a központi telepítési helye az erőforráscsoport:

    "variables": {
      ...
      "location": "[resourceGroup().location]",
      ...
    }

Majd ezen a helyen minden erőforrás egy adott labor-példány:

    "resources": [
      {
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[variables('location')]",
        ...
      },
      {
        "type": "Microsoft.Network/publicIPAddresses",
        "location": "[variables('location')]",
        ...
      },
      {
        "type": "Microsoft.Network/virtualNetworks",
        "location": "[variables('location')]",
        ...
      },
      {
        "type": "Microsoft.Network/networkInterfaces",
        "location": "[variables('location')]",
        ...
      },
      {
        "type": "Microsoft.Compute/virtualMachines",
        "location": "[variables('location')]",
        ...
      }
    ]

Győződjön meg arról, hogy engedélyezett-e az előfizetés tulajdonosaként tulajdonképpen kiválasztja régióban üzembe helyezni kívánt erőforrások üzembe helyezéséhez, szüksége. Szüksége győződjön meg arról, hogy a virtuálisgép-rendszerképek érhetők el szeretné engedélyezni, minden régióban, ellenkező esetben a központi telepítési sablont nem működnek az egyes régiókban.

### <a name="outputs"></a>Kimenetek

Általában Resource Manager-sablonok, üzembe helyezheti nélkül állít elő kimenetet. Ennek oka, hogy ismeri az összes Sablonparaméterek tölti ki értéket, és bármely erőforrás tulajdonságainak mindig manuálisan ellenőrizheti.

A Test Drive Resource Manager-sablonok azonban azt\'s fontos, hogy térjen vissza Test Drive minden az adatokat, amelyek a laborhoz (webhely URI-k, a virtuális gép állomásnevek, felhasználói neveket és jelszavakat) hozzáférés szükséges. Ellenőrizze, hogy az összes kimeneti név olvashatók legyenek, mivel ezeket a változókat az ügyfélnek.

Nem vonatkoznak minden olyan korlátozások kapcsolatos sablon következő kimeneteit. Ne feledje, minden kimenetének értékeket cserélheti le Test Drive alakíthatók át egymásba **karakterláncok**, így a kimeneti objektum küld, ha a felhasználó megjelenik-e JSON karakterlánc.

Példa:

    "outputs": {
      "Host Name": {
        "type": "string",
        "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
      },
      "User Name": {
        "type": "string",
        "value": "[parameters('adminName')]"
      },
      "Password": {
        "type": "string",
        "value": "[parameters('adminPassword')]"
      }
    }

### <a name="subscription-limits"></a>Előfizetés korlátai

Egy dolog, figyelembe kell venni az előfizetés és szolgáltatási korlátait. Például ha azt szeretné, akár tíz 4 magos virtuális gépek üzembe helyezéséhez, szüksége, hogy a labor használt előfizetés lehetővé teszi, hogy 40 mag.

További információ az Azure-előfizetések és -szolgáltatások korlátozásait, az annak [Ez a cikk](https://docs.microsoft.com/azure/azure-subscription-service-limits). Mivel több Tesztverzió elvégezhet egy időben, győződjön meg arról, hogy az előfizetés képes kezelni a \# egyidejű Tesztverzió elvégezhet teljes száma szorozva magok.

### <a name="what-to-upload"></a>Mi a feltöltése

Test Drive Resource Manager-sablon, egy zip-fájlt, amely különböző üzembe helyezési összetevők is tartalmazhat, de rendelkeznie kell egy fájlt töltenek fel **main-template.json**. Ezt a fájlt az Azure Resource Manager üzembe helyezési sablon, és a Test Drive segítségével a labor-példányt létrehozni.

Ha ez a fájl túl további erőforrások, a sablonban lévő külső erőforrásként hivatkozhasson rá, vagy az erőforrás belefoglalhatja a zip-fájlt.

A közzétételi hitelesítő során a Test Drive unzips a központi telepítési csomagot, és helyezi a tartalmat egy belső Test Drive blobtárolóba. Tároló struktúráját tükrözi a központi telepítési csomag szerkezete:

| Package.zip                       | Test Drive blob-tároló         |
|---|---|
Main-template.json                | [https://\<\.... \>.blob.core.windows.net/\<\.... \>/main-template.json](#)  |
 Templates/Solution.JSON           | [https://\<\.... \>.blob.core.windows.net/\<\.... \>/templates/solution.json](#) |
| scripts/warmup.ps1                | [https://\<\.... \>.blob.core.windows.net/\<\.... \>/scripts/warmup.ps1](#)  |


Ez a blobtároló alap URI-t egy URI-nevezzük. Minden változatban a labor létrehozása a saját blobtárolót rendelkezik, és ezért a minden változatban a labor létrehozása a saját alap Uri tartozik. Test Drive továbbíthatja egy alap Uri-ját a kicsomagolt központi telepítési csomagot a sablonhoz a sablon paraméterei.

<a name="transforming-template-examples-for-test-drive"></a>A Test Drive alkalmazásán át a sablon példák
---------------------------------------------

A folyamat ne tudják bekapcsolni az architektúra az erőforrások Test Drive Resource Manager-sablonnal történő ijesztőnek tűnhet. Segítse a folyamat megkönnyítése, azt\'tenni példákkal arról, hogyan legjobb [aktuális központi telepítési sablonok itt átalakítása](./transforming-examples-for-test-drive.md).

<a name="how-to-publish-a-test-drive"></a>A Test Drive közzététele
---------------------------

Most, hogy a beépített Test Drive, ez a szakasz végigvezeti Önt a mezőkben ahhoz, hogy a Test Drive közzététele sikertelen.

![Próbálja ki a felhasználói felületen engedélyezése](./media/azure-resource-manager-test-drive/howtopub1.png)

Az első és legfontosabb mezőt, hogy állíthatja be, hogy azt szeretné, hogy a Test Drive az ajánlatban engedélyezve van-e. Ha bejelöli **Igen,** a képernyőn az összes kötelező mezőt a többi jelennek meg, hogy adja meg. Ha bejelöli **nem,** az űrlap le lesz tiltva, és ha ismét közzéteszi az a Tesztverziós le van tiltva, a Test Drive éles törlődik.

Megjegyzés: Ha bármely felhasználó által aktívan használt tesztek meghajtók, ezeket a Tesztverzió továbbra is futni, amíg a munkamenet lejár.

### <a name="details"></a>Részletek

Töltse ki a következő szakaszban a Test Drive részleteit kínálnak.

![Test Drive részletes információk](./media/azure-resource-manager-test-drive/howtopub2.png)

**Leírás –** *szükséges* Ez az, ahol ír a fő leírást arról, mi van a Test Drive. Az ügyfél milyen helyzetekben a Test Drive fog lefedő, a termékkel kapcsolatos elolvasható itt fognak érkezni. 

**Felhasználó manuális –** *szükséges* Ez az a Test Drive felhasználói élmény részletes leírását. Az ügyfél megnyílik ez, és pontosan mit azt szeretné, hajtsa végre a Test Drive alatt is végig. Fontos, hogy ez a tartalom így könnyen megismerhető és hajtsa végre a! (.Pdf fájlnak kell lennie)

**Tesztelje a meghajtó – bemutató videó -** *ajánlott* hasonló felhasználói kézikönyvet, a legjobb, ha egy videó-oktatóanyag, a Test Drive felhasználói élmény. Az ügyfél figyelni ezen előzetes vagy a Test Drive során, és pontosan mit azt szeretné, hajtsa végre a Test Drive alatt is végig. Fontos, hogy ez a tartalom így könnyen megismerhető és hajtsa végre a!

- **Név** – a videó címe
- **Hivatkozás** -kell lennie egy beágyazott cső vagy videó URL-CÍMÉT. A beágyazott URL-cím beszerzése a példában nem éri el:
- **Miniatűr** -képnek kell lennie egy kiváló minőségű (533 x 324) képpont. Javasoljuk, hogy egy része a Test Drive felhasználói élmény képernyőképe itt is.

Az alábbi, hogyan ezek a mezők meg az ügyfelek számára a Test Drive során.

![A Marketplace-ajánlat a Test Drive mezők helyét](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Technikai konfiguráció

Töltse ki a következő szakaszban, ahol, töltse fel a Test Drive Resource Manager-sablont, és hogyan kifejezetten határozza meg a Test Drive instances munka.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**Példányok –** *szükséges* itt konfigurálhatja, hogy hány példánya ez szeretne, mit régió(k) a, és az ügyfelek milyen gyorsan kérheti le a Test Drive.

- **Példányok** -régiók kiválasztása a, ahol, válassza ki a Test Drive Resource Manager-sablon a telepítési helyét. Javasoljuk, hogy egyszerűen válasszon ki egy adott régióban, ahol várhatóan leggyakrabban az ügyfelei számára, a következő helyen található.
- **Gyakori elérésű** -Test Drive-példányok, amelyek már központilag telepítve és Várakozás a kiválasztott régiónként eléréséhez. Ügyfelek azonnal hozzáférhetnek a Tesztverzió várnia a központi telepítés helyett. A hosszabb, hogy ezek a példányok mindig futtat az Azure-előfizetést, akkor számítunk fel a költségek nagyobb rendelkezésre állását. Erősen javasoljuk, hogy **legalább egy gyakran használt adatok példány**, mint az ügyfelek többsége nem szeretne várni teljes központi telepítés befejezéséhez, és így az ügyfelek általi használatot egy Gyűjtőtár.
- **Meleg** - Test Drive-példányok alkalmazott régiónként és majd a virtuális gép lett leállítva és az Azure storage tárolja. A várakozási idő a meleg példányokhoz lassabb, mint a gyakori elérésű példányok, de hasznos üzemidőt, a tárolási költség is kevesebb költséggel jár.
- **Ritkán használt** -Test Drive-példányok, valószínűleg telepíthető régiónként. Ritkán használt példány a teljes teszt meghajtó Resource Manager-sablon egy ügyfél a Test Drive kér, ezért létfontosságú a lassabb, mint a gyakori vagy a meleg példányok a időpontban központi telepítési lépés szükséges. Azonban az egyensúlyt a is, hogy csak akkor kell fizetnie, a Test Drive időtartama.

Jelenleg számítja ki a teljes száma a lehetséges egyidejű Tesztverzió fog elérhetővé, és győződjön meg arról, hogy az előfizetéshez tartozó kvótahatárt képes kezelni a párhuzamos összeg:

**(Száma a kiválasztott régióban x gyakori elérésű példányok) + (szám / régiókban kijelölt x meleg példányok) + (szám / régiók kijelölt x hideg példányok)**

**Tesztelje a meghajtó időtartama (óra) –** *szükséges* mennyi ideig a Test Drive aktív marad, az időtartam \# óra. A Test Drive Ez az időtartam lejárta után automatikusan leáll.

**Test Drive Resource Manager-sablon –** *szükséges* Itt a Resource Manager-sablon feltöltése. Ez az a fenti az előző szakaszban létrehozott fájlt. A fő sablon fájl neve: "main-template.json", és győződjön meg arról, hogy a Resource Manager-sablon szükséges kulcs változókat kimeneti paramétereket tartalmaz. (.Zip kiterjesztésű fájlnak kell lennie)

**Információ - hozzáférés** *szükséges* után az ügyfél lekéri a Test Drive, a hozzáférési információk jelennek-e meg a számukra. Ezeket az utasításokat úgy van kialakítva, hogy a hasznos a kimeneti paraméterek a Test Drive Resource Manager-sablon megosztása. Adja meg a kimeneti paraméterek, használja a kapcsos zárójelek (például **{{outputname}}**), és azok kerül beillesztésre megfelelően a helyen. (HTML karakterlánc-formátum ajánlott Itt jelennek meg az előtér).

### <a name="test-drive-deployment-subscription-details"></a>Tesztelés üzembe helyezési előfizetés részletei

Az utolsó szakaszban adja meg, hogy tud érvénybe léptetni a Tesztverzió automatikusan csatlakozzon az Azure-előfizetés és az Azure Active Directory (AD).

![Teszt telepítési előfizetés részletei](./media/azure-resource-manager-test-drive/subdetails1.png)

**Az Azure előfizetés-azonosító –** *szükséges* Ez hozzáférést biztosít az Azure-szolgáltatások és az Azure Portalon. Az előfizetés, ahol készüljön jelentés, erőforrás-használat és a szolgáltatás számlázása. Ha Ön még nem rendelkezik egy **külön** Azure előfizetés Tesztverzió csak, lépjen tovább, és hozzon létre egyet. Az Azure-előfizetés azonosítókat megkereséséhez az Azure Portalra jelentkezik be, és ellenőrizheti, hogy az előfizetések, a bal oldali menüben. (Példa: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Az Azure-előfizetések](./media/azure-resource-manager-test-drive/subdetails2.png)

**Az Azure AD-bérlő azonosítója –** *szükséges* Ha a bérlő Azonosítóját már rendelkezésre álló tulajdonságok, azt alább találhatja\> címtár-azonosító

![Az Azure Active Directory-tulajdonságok](./media/azure-resource-manager-test-drive/subdetails3.png)

Máskülönben hozzon létre egy új bérlőt az Azure Active Directoryban.

![A lista az Azure Active Directory-bérlők](./media/azure-resource-manager-test-drive/subdetails4.png)

![Az Azure AD-bérlő a szervezet, a tartomány és az ország megadása](./media/azure-resource-manager-test-drive/subdetails5.png)

![A kijelölés megerősítéséhez](./media/azure-resource-manager-test-drive/subdetails6.png)

**Az Azure AD-Alkalmazásazonosító -** *szükséges* hozhat létre, és a egy új alkalmazás regisztrálása a következő lépéssel. Ezt az alkalmazást használjuk a Test Drive-példány műveletek végrehajtásához.

1. Lépjen abba a könyvtárba, újonnan létrehozott vagy már meglévő könyvtár, és válassza ki az Azure Active directory szűrő ablaktáblát.
2. Keressen az "Alkalmazásregisztrációk", és kattintson a "Hozzáadás" gombra
3. Adjon meg egy alkalmazásnevet.
4. Adja meg, mint "webalkalmazás / API"
5. Adja meg a bejelentkezési URL-értéket, megnyert\'t használja ezt a mezőt.
6. Kattintson a Létrehozás gombra.
7. Miután létrehozta az alkalmazást, válassza a Tulajdonságok –\> állítja be az alkalmazás több-bérlős, és kattintson a Mentés gombra.

Kattintson a Mentés gombra. Az utolsó lépés, hogy az Alkalmazásazonosítót, a regisztrált alkalmazás megnyitása, és illessze be ide a Test Drive mező.

![Az Azure AD application ID részletei](./media/azure-resource-manager-test-drive/subdetails7.png)

Adott használjuk az alkalmazás üzembe helyezéséhez az előfizetéshez, hozzá kell adnunk a az alkalmazás az előfizetés közreműködője. A következő útmutatót: ezek a következők alatt:

1. Az előfizetések panelen keresse meg, és válassza ki a megfelelő előfizetést, amely csak a Test Drive használja.
1. Kattintson a **hozzáférés-vezérlés (IAM)**.
1. Kattintson a **szerepkör-hozzárendelések** fülre.  ![Adjon hozzá egy új hozzáférés-vezérlés egyszerű](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Kattintson a **szerepkör-hozzárendelés hozzáadása**.
1. Állítsa be a szerepkört, **közreműködői**.
1. Írja be az Azure AD-alkalmazás nevét, és válassza ki a szerepkör hozzárendelése az alkalmazást.
    ![Az engedélyek hozzáadása](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Kattintson a **Save** (Mentés) gombra.

**Az Azure AD-Alkalmazáskulcs -** *szükséges* az utolsó mezőt, hogy a hitelesítési kulcs létrehozásához. A kulcsok adjon meg egy kulcs leírást az időtartamot, soha nem jár le, majd válassza a mentés. Ez **fontos** ne kelljen egy lejárt kulcs, amely megszakítja a test drive éles környezetben. Másolja ezt az értéket, és illessze be a Test Drive kötelező mező.

![A kulcsok az Azure AD-alkalmazás látható](./media/azure-resource-manager-test-drive/subdetails8.png)

<a name="next-steps"></a>További lépések
----------

Most, hogy az összes a Test Drive mezőket kitölteni, mennek keresztül és **tegye közzé újra** az ajánlatot. A Test Drive hitelesítő letelte után meg kell haladnia egy alaposan tesztelje az ügyfélélmény a **előzetes** az Ön ajánlatát. Indítsa el a Test Drive a felhasználói felületen, majd nyissa meg az Azure előfizetését az Azure Portalon, és győződjön meg arról, hogy a Tesztverzió rendszer teljes mértékben telepíti megfelelően.

![Azure Portal](./media/azure-resource-manager-test-drive/subdetails9.png)

Fontos megjegyezni, hogy nem törli bármely Test Drive instances, kiépítésüket ügyfelei, így az ügyfél azt befejezése után a Test Drive szolgáltatás automatikusan tiszta ezeket az erőforráscsoportokat.

Ha úgy gondolja, hogy tisztában az előzetes verzió ajánlathoz, most már, ideje **élesben**! Van a Microsoft végső lektorálásra folyamat után az ajánlat már közzétett kettős ellenőrizze a teljes teljes körű felhasználói élményt. Ha valamilyen okból az ajánlat beolvasása visszautasítja, küldeni fogunk egy értesítést a engineering contact az ajánlatban elmagyarázza, mit lehet javítani kell.

Ha további kérdése van, hibaelhárítási tanácsokat keres, vagy kívánja-e a Test Drive sokkal sikeresebb lenne, nyissa meg [– gyakori kérdések, hibaelhárítási és ajánlott eljárások](./marketing-and-best-practices.md).
