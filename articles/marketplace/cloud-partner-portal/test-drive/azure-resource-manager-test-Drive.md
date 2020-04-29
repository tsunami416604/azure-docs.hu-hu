---
title: Azure Resource Manager tesztelési meghajtó | Azure piactér
description: Piactéri tesztelési meghajtó létrehozása Azure Resource Manager használatával
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6125aa010d8676518b84f866343b01f95246160f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275934"
---
# <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager – Tesztverzió

Ez a cikk azoknak a kiadóknak szól, akik rendelkeznek az Azure Marketplace-en, vagy akik a AppSource, de csak Azure-erőforrásokkal szeretnék felépíteni a tesztelési meghajtót.

A Azure Resource Manager (Resource Manager) sablon az Azure-erőforrások olyan kódolt tárolója, amelyet a legjobban a megoldásához terveztek. Ha nem ismeri a Resource Manager-sablonokat, olvassa el a [Resource Manager-sablonok ismertetése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) és a [Resource Manager-sablonok készítése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) című témakört, amelyből megtudhatja, hogyan hozhat létre és tesztelheti saját sablonjait.

Mi a tesztvezetés, hogy a megadott Resource Manager-sablont veszi át, és a Resource Manager-sablonból szükséges összes erőforrást egy erőforráscsoporthoz helyezi üzembe.

Ha úgy dönt, hogy létrehoz egy Azure Resource Manager tesztelési meghajtót, a követelmények a következők:

- Hozza létre, tesztelje, majd töltse fel a test Drive Resource Manager-sablont.
- Konfigurálja az összes szükséges metaadatot és beállítást a tesztelési meghajtó engedélyezéséhez.
- Az ajánlat ismételt közzététele engedélyezve van a test Drive szolgáltatásban.

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>Azure Resource Manager tesztelési meghajtó létrehozása

A Azure Resource Manager tesztvezetés létrehozási folyamata:

1. Tervezze meg, mit szeretne tenni az ügyfeleknek egy folyamat diagramon.
1. Határozza meg, hogy milyen tapasztalatokat szeretne készíteni ügyfelei számára.
1. A fenti definíciók alapján döntse el, hogy mely eszközökre és erőforrásokra van szükség ahhoz, hogy az ügyfelek el tudjanak végezni az ilyen jellegű felhasználói élményt: például D365-példány vagy egy adatbázissal rendelkező webhely.
1. Hozza létre a kialakítást helyileg, és tesztelje a felhasználói élményt.
1. Egy ARM-sablon üzembe helyezése során becsomagolhatja a felhasználói élményt:
    1. Meghatározza, hogy az erőforrások mely részei legyenek bemeneti paraméterek;
    1. Milyen változók;
    1. Milyen kimeneteket kapnak a felhasználói élmény.
1. Közzététel, tesztelés és élő indítás.

A Azure Resource Manager tesztvezetés létrehozásának legfontosabb része az ügyfelek által tapasztalható forgatókönyv (ek) meghatározása. Ön egy tűzfal termék, és szeretné bemutatni, milyen jól kezeli a parancsfájl-befecskendezéses támadásokat? Ön egy tárolási termék, és szeretné bemutatni, hogy a megoldás milyen gyorsan és egyszerűen tömöríti a fájlokat?

Ügyeljen arra, hogy elegendő időt töltsön ki a termék megjelenítésének legjobb módjainak kiértékelésével. Kifejezetten az összes szükséges erőforrásra vonatkozóan, mivel a Resource Manager-sablon megfelelő csomagolását teszi lehetővé.

A tűzfal példájának folytatásához az architektúra az lehet, hogy szüksége van egy nyilvános IP URL-címre a szolgáltatáshoz és egy másik nyilvános IP-címet a tűzfal által védett webhelyhez. Minden IP-cím egy virtuális gépen van telepítve, és egy hálózati biztonsági csoport + hálózati adapterrel van csatlakoztatva.

Miután megtervezte a kívánt erőforrás-csomagot, most megjelenik a test Drive Resource Manager-sablon írása és létrehozása.

## <a name="writing-test-drive-resource-manager-templates"></a>A test Drive Resource Manager-sablonok írása

A tesztvezetés teljesen automatizált módban futtatja az üzemelő példányokat, ezért a test Drive-sablonokhoz az alábbiakban ismertetett korlátozások vonatkoznak.

### <a name="parameters"></a>Paraméterek

A legtöbb sablonhoz paraméterek vannak megadva. A paraméterek az erőforrásnevek, az erőforrások mérete (például a Storage-fiókok vagy a virtuális gépek méretei), a felhasználónevek és a jelszavak, a DNS-nevek és így tovább definiálják a paramétereket. Ha Azure Portal használatával telepít megoldásokat, manuálisan is feltöltheti ezeket a paramétereket, kiválaszthatja a rendelkezésre álló DNS-neveket vagy a Storage-fiókok nevét, és így tovább.

![A Azure Resource Manager paramétereinek listája](./media/azure-resource-manager-test-drive/param1.png)

A test Drive azonban teljesen automatikus módban működik, emberi beavatkozás nélkül, így csak korlátozott számú paramétert támogat. Ha a test Drive Resource Manager-sablon egyik paramétere nem tartozik a támogatott kategóriák valamelyikébe, a **paramétert változó vagy konstans értékkel kell helyettesítenie.**

Bármelyik érvényes nevet használhatja a paraméterekhez, a test Drive felismeri a paraméter kategóriáját a metaadat típusú érték használatával. **Minden sablon paraméterhez meg kell adni a metaadat-típust**, ellenkező esetben a sablon nem fogja átadni az ellenőrzést:

```json
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
```

Azt is fontos megjegyezni, hogy az **összes paraméter nem kötelező**, így ha\'nem szeretné használni a t,\'nem kell.

### <a name="accepted-parameter-metadata-types"></a>Elfogadott paraméterek metaadatainak típusai

| Metaadat típusa   | Paraméter típusa  | Leírás     | Minta értéke    |
|---|---|---|---|
| **BaseUri**     | sztring          | A központi telepítési csomag alap URI-ja| https:\//\<.\. \>. blob.Core.Windows.net/\<\..\> |
| **username**    | sztring          | Új véletlenszerű Felhasználónév.| admin68876      |
| **alaphelyzetbe állítása**    | biztonságos karakterlánc    | Új véletlenszerű jelszó | LP! ACS\^2kh     |
| **munkamenet-azonosító**   | sztring          | Egyedi tesztelési meghajtó munkamenet-azonosítója (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>BaseUri

A test Drive inicializálja ezt a paramétert a központi telepítési csomag **alapszintű URI-ja** használatával, így ezzel a paraméterrel a csomagban található bármely fájl URI-ját is létrehozhatja.

```json
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
```

A sablonban ezt a paramétert használhatja bármely fájl URI-azonosítójának létrehozásához a test Drive-alapú központi telepítési csomagból. Az alábbi példa bemutatja, hogyan hozhat létre egy URI-t a csatolt sablonhoz:

```json
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>felhasználónév

A test Drive inicializálja ezt a paramétert egy új véletlenszerű felhasználónévvel:

```json
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
```

Minta értéke:

    admin68876

A megoldáshoz véletlenszerű vagy állandó felhasználónevet is használhat.

#### <a name="password"></a>jelszó

A test Drive inicializálja ezt a paramétert egy új véletlenszerű jelszóval:

```json
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
```

Minta értéke:

    Lp!ACS^2kh

A megoldáshoz véletlenszerű vagy állandó jelszavakat is használhat.

#### <a name="session-id"></a>munkamenet-azonosító

A test Drive inicializálja ezt a paramétert egy olyan egyedi GUID azonosítóval, amely a teszt meghajtó munkamenet-AZONOSÍTÓját jelképezi

```json
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
```

Minta értéke:

    b8c8693e-5673-449c-badd-257a405a6dee

Ezzel a paraméterrel egyedileg azonosíthatja a tesztelési meghajtó munkamenetét, ha szükséges.

### <a name="unique-names"></a>Egyedi nevek

Egyes Azure-erőforrások, például a Storage-fiókok vagy a DNS-nevek globálisan egyedi neveket igényelnek.

Ez azt jelenti, hogy minden alkalommal, amikor a test Drive üzembe helyezi a Resource Manager-sablont, létrehoz egy **új erőforráscsoportot** , amely\' minden erőforrásához egyedi névvel rendelkezik. Ezért a [uniquestring](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) függvényt kell használnia az erőforráscsoport-azonosítókban szereplő változók neveivel, hogy véletlenszerű egyedi értékeket állítson elő:

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Ügyeljen arra, hogy a paraméter/változó sztringeket (\'contosovm\') egyedi karakterlánc-kimenettel (\'resourceGroup (). id\') fűzze össze, mivel ez garantálja az egyes változók egyediségét és megbízhatóságát.

Például a legtöbb erőforrás neve nem kezdődhet számjegyekkel, de az egyedi karakterlánc függvény egy számjegyből álló karakterláncot ad vissza. Ha tehát nyers egyedi karakterlánc-kimenetet használ, a központi telepítések sikertelenek lesznek. 

Az erőforrás-elnevezési szabályokkal és korlátozásokkal kapcsolatos további információkat [ebben a cikkben](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)talál.

### <a name="deployment-location"></a>Központi telepítés helye

A tesztelési meghajtót különböző Azure-régiókban is elérhetővé teheti. Az a gondolat, hogy lehetővé teszi a felhasználó számára a legközelebbi régió kiválasztását a Beast felhasználói élmény biztosításához.

Amikor a test Drive létrehozza a labor egy példányát, az mindig létrehoz egy erőforráscsoportot egy felhasználó által választott régióban, majd végrehajtja a telepítési sablont a csoport környezetében. Ezért a sablonnak ki kell választania a központi telepítési helyet az erőforráscsoporthoz:

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

Ezután használja ezt a helyet minden erőforráshoz egy adott labor-példány esetében:

```json
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
```

Győződjön meg arról, hogy az előfizetése jogosult a kiválasztott régiókban üzembe helyezni kívánt összes erőforrás üzembe helyezésére. Emellett meg kell győződnie arról, hogy a virtuálisgép-lemezképek elérhetők az összes engedélyezni kívánt régióban, ellenkező esetben a telepítési sablon egyes régiókban nem fog működni.

### <a name="outputs"></a>Kimenetek

A Resource Manager-sablonokkal általában bármilyen kimenet létrehozása nélkül is üzembe helyezhető. Ennek az az oka, hogy ismeri az összes olyan értéket, amelyet a sablon paramétereinek feltöltéséhez használ, és bármikor manuálisan megvizsgálhatja bármelyik erőforrás tulajdonságait.

A test Drive Resource Manager-sablonok esetében azonban\'fontos, hogy térjen vissza a test Drive összes adatához, amely a laborhoz való hozzáféréshez szükséges (webhely URI-k, virtuális gépek állomásneve, felhasználónevek és jelszavak). Győződjön meg arról, hogy az összes kimeneti név olvasható, mert ezek a változók az ügyfél számára jelennek meg.

Nincsenek a sablon kimenetével kapcsolatos korlátozások. Ne feledje, hogy a test Drive minden kimeneti értéket **sztringbe**konvertál, így ha egy objektumot küld a kimenetre, a felhasználó a JSON-karakterláncot fogja látni.

Példa:

```json
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
```

### <a name="subscription-limits"></a>Előfizetési korlátok

Még egy dolog, amit figyelembe kell vennie az előfizetés és a szolgáltatás korlátai. Ha például akár tíz 4 magos virtuális gépet szeretne üzembe helyezni, győződjön meg arról, hogy a laborhoz használt előfizetés lehetővé teszi a 40 magok használatát.

[Ebben a cikkben](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)további információt talál az Azure-előfizetéssel és a szolgáltatási korlátozásokkal kapcsolatban. Egyszerre több tesztvezetés is elvégezhető. Ellenőrizze, hogy az előfizetés képes-e kezelni a \# magok számát, szorozva az egyidejű tesztelési meghajtók teljes számával.

### <a name="what-to-upload"></a>Mi a feltöltés?

A test Drive Resource Manager-sablon zip-fájlként lett feltöltve, amely különböző üzembe helyezési összetevőket tartalmazhat, de rendelkeznie kell egy **Main-template. JSON**nevű fájllal. Ez a fájl Azure Resource Manager telepítési sablon, a test Drive pedig egy labor létrehozásához használja.

Ha a fájlon kívül további erőforrásokkal is rendelkezik, hivatkozhat külső erőforrásként a sablonban, vagy felveheti az erőforrást a zip-fájlba.

A közzétételi minősítés során a test Drive kibontja a telepítési csomagot, és egy belső tesztvezetés blob-tárolóba helyezi a tartalmát. A tároló szerkezete a telepítési csomag szerkezetét tükrözi:

| Package. zip                       | Tesztvezetés blob-tárolója         |
|---|---|
| Main-template. JSON                | \//\<https:\... \>. blob.Core.Windows.net/\<\... \>/Main-template.JSON  |
| sablonok/megoldás. JSON           | \//\<https:\... \>. blob.Core.Windows.net/\<\... \>/templates/Solution.JSON |
| Scripts/bemelegedési. ps1                | \//\<https:\... \>. blob.Core.Windows.net/\<\... \>/Scripts/warmup.ps1  |


Meghívjuk a blob Container alap URI azonosítójának URI-ját. A tesztkörnyezet minden változatának saját blob-tárolója van, ezért a labor minden változata saját alap URI-val rendelkezik. A test Drive a sablon paramétereinek használatával átadhatja a kibontott telepítési csomag alapszintű URI-ját a sablonba.

## <a name="transforming-template-examples-for-test-drive"></a>Példák a tesztelési meghajtón a sablon átalakítására

Az erőforrások architektúrájának a test Drive Resource Manager-sablonba való bekapcsolásának folyamata ijesztő lehet. Ennek a folyamatnak a megkönnyítése érdekében példákat\'készítettünk arra, hogyan [alakíthatja át az aktuális üzembe helyezési sablonokat](./transforming-examples-for-test-drive.md)a legjobb megoldásként.

## <a name="how-to-publish-a-test-drive"></a>Tesztelési meghajtó közzététele

Most, hogy elkészítette a tesztelési meghajtót, ez a szakasz végigvezeti a tesztelési meghajtó sikeres közzétételéhez szükséges összes mezőn.

![A test Drive engedélyezése a felhasználói felületen](./media/azure-resource-manager-test-drive/howtopub1.png)

Az első és legfontosabb mező az, hogy be kell-e állítani, hogy a test Drive engedélyezve van-e az ajánlathoz. Ha az Igen lehetőséget választja **,** az űrlap többi részét az összes kötelező mezővel kitöltheti. Ha a nem lehetőséget választja **,** az űrlap le lesz tiltva, és ha újból közzéteszi a tesztelési meghajtót, a rendszer eltávolítja a tesztelési meghajtót az éles környezetből.

Megjegyzés: ha vannak olyan tesztek, amelyeket aktívan használnak a felhasználók, ezek a tesztelési meghajtók továbbra is futni fognak, amíg a munkamenet le nem jár.

### <a name="details"></a>Részletek

A kitöltendő következő szakasz a tesztelési meghajtó ajánlatának részleteit tartalmazza.

![A tesztvezetés részletes adatai](./media/azure-resource-manager-test-drive/howtopub2.png)

**Leírás –** *itt kell* megírnia a tesztelési meghajtón található fő leírást. Az ügyfél itt olvashatja el, hogy milyen forgatókönyveket fog a tesztvezetés a termékre vonatkozóan. 

**Felhasználói kézikönyv –** *a* tesztelési teljesítmény részletes útmutatója. Az ügyfél ekkor megnyithatja ezt, és pontosan megtekintheti, hogy mit szeretne tenni a tesztelési meghajtón. Fontos, hogy ez a tartalom könnyen érthető és követhető legyen! (. PDF-fájlnak kell lennie)

A **teszt Drive bemutató videója –** a felhasználói kézikönyvhez hasonló módon *ajánlott* , a legjobb megoldás, ha a tesztelési meghajtóval kapcsolatos videós oktatóanyagot tartalmaz. Az ügyfél ezt megelőzően vagy a tesztelési meghajtón tekinti meg, és pontosan megmutatja, mit szeretne tenni a tesztelési meghajtón. Fontos, hogy ez a tartalom könnyen érthető és követhető legyen!

- A videó **neve** – cím
- **Hivatkozás** – a cső vagy videó beágyazott URL-címének kell lennie. Példa a beágyazott URL-cím beszerzésére:
- **Miniatűr** – magas minőségi képpontnak (533x324) kell lennie. A test Drive-élmény néhány részének képernyőképét javasoljuk.

Alább látható, hogyan jelennek meg ezek a mezők az ügyfél számára a test Drive-élményben.

![A test Drive-mezők helye a Piactéri ajánlatban](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Technikai konfiguráció

A kitöltés következő szakasza a test Drive Resource Manager-sablon feltöltése, valamint a tesztvezetés-példányok működésének meghatározása.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**Példányok –** *itt* megadhatja, hogy hány példányt szeretne, milyen régió (k) ban, és hogy az ügyfelek milyen gyorsan kapják meg a tesztelési meghajtót.

- **Példányok** – a válassza ki a régiókat, ahol kiválaszthatja a test Drive Resource Manager-sablon üzembe helyezésének helyét. Azt javasoljuk, hogy csak egy olyan régiót válasszon ki, ahol a legnagyobb elvárás, hogy az ügyfelek hol találhatók.
- **Hot** A már üzembe helyezett és a kiválasztott régióhoz való hozzáférésre váró tesztelési meghajtók példányainak száma. Az ügyfelek azonnal hozzáférhetnek a tesztelési meghajtókhoz, és nem kell megvárniuk az üzembe helyezést. A kompromisszum azt eredményezi, hogy ezek a példányok mindig az Azure-előfizetésen futnak, így a költségek nagyobb üzemidőt jelentenek. Erősen ajánlott **legalább egy gyors példányt**használni, mivel az ügyfelek többsége nem szeretné megvárni a teljes üzembe helyezést, és így az ügyfél-használatban van egy legördülő lista.
- **Warm** Az üzembe helyezett, régiónként üzemelő, és az Azure Storage-ban tárolt tesztelési meghajtó példányainak száma. A meleg példányok várakozási ideje lassabb, mint a gyakran használt példányok száma, de a tárterületre vonatkozó üzemidő ára is kevésbé költséges.
- **Cold** A valószínűleg üzembe helyezhető, régión belüli tesztelési meghajtók példányainak száma. A hideg példányok esetében a teljes test Drive Resource Manager-sablonnak meg kell haladnia egy központi telepítésen a tesztelési meghajtót kérő ügyfélen, hogy lassabb legyen, mint a gyors vagy a meleg példány. A kompromisszum azonban az, hogy csak a tesztvezetés időtartamára kell fizetnie.

Ekkor kiszámítja az elérhetővé tenni kívánt egyidejű tesztelési meghajtók teljes számát, és ellenőrzi, hogy az előfizetéshez tartozó kvóta korlátja képes-e az egyidejű mennyiség kezelésére:

**(A kiválasztott régiók száma x forró példányok esetén) + (a kiválasztott régiók száma x meleg példányok esetén) +**

**Tesztelési meghajtó időtartama (óra) –** az a *szükséges* időtartam, \# ameddig a tesztvezetés aktív marad (óra). A tesztelési meghajtó automatikusan leáll az adott időszak lejárta után.

**Test Drive Resource Manager-sablon –** ide *kell* feltöltenie a Resource Manager-sablont. Ez az előző szakaszban létrehozott fájl. A fő sablonfájl neve: "Main-template. JSON", és győződjön meg arról, hogy a Resource Manager-sablon tartalmazza a szükséges kulcs-változók kimeneti paramétereit. (. Zip-fájlnak kell lennie)

**Hozzáférési információk –** az ügyfél által a tesztelési meghajtó beszerzése *után a* hozzáférési információk jelennek meg. Ezek az utasítások célja, hogy megosszák a hasznos kimeneti paramétereket a test Drive Resource Manager-sablonból. A kimeneti paraméterek belefoglalásához használjon dupla kapcsos zárójeleket (például **{{outputname}}**), és a helyükön helyesen lesznek beszúrva. (A HTML-karakterlánc formázását érdemes az előtérben megjeleníteni).

### <a name="test-drive-deployment-subscription-details"></a>Tesztelési meghajtó üzembe helyezésének előfizetése – részletek

A kitöltendő utolsó szakasz az Azure-előfizetés és a Azure Active Directory (AD) összekapcsolásával automatikusan képes lesz a tesztelési meghajtók üzembe helyezésére.

![Tesztelési meghajtó üzembe helyezésének előfizetése – részletek](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure-előfizetés azonosítója –** ehhez hozzáférést kell *adnia* az Azure-szolgáltatásokhoz és a Azure Portalhoz. Az előfizetés az erőforrás-használat jelentését és a szolgáltatások számlázását jelenti. Ha még nem rendelkezik **külön** Azure-előfizetéssel csak tesztelési meghajtókhoz, folytassa a következő lépéssel. Az Azure-előfizetési azonosítók megkereséséhez jelentkezzen be Azure Portal és navigáljon a bal oldali menüben lévő előfizetésekhez. (Példa: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Azure-előfizetések](./media/azure-resource-manager-test-drive/subdetails2.png)

**Azure ad-bérlő azonosítója –** *kötelező* , ha már elérhető bérlői azonosító, a Properties-Directory-\> azonosítóban található.

![Azure Active Directory tulajdonságai](./media/azure-resource-manager-test-drive/subdetails3.png)

Ellenkező esetben hozzon létre egy új bérlőt Azure Active Directory.

![Azure Active Directory bérlők listája](./media/azure-resource-manager-test-drive/subdetails4.png)

![Az Azure AD-bérlő szervezetének, tartományának és országának/régiójának megadása](./media/azure-resource-manager-test-drive/subdetails5.png)

![A kijelölés megerősítése](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure ad alkalmazás azonosító –** *kötelező* következő lépés egy új alkalmazás létrehozása és regisztrálása. Ezt az alkalmazást fogjuk használni a test Drive-példányon végrehajtott műveletek végrehajtásához.

1. Navigáljon az újonnan létrehozott címtárhoz vagy már meglévő címtárhoz, és válassza az Azure Active Directory elemet a szűrő ablaktáblán.
2. Keressen rá a "Alkalmazásregisztrációk" kifejezésre, és kattintson a "Hozzáadás" gombra.
3. Adja meg az alkalmazás nevét.
4. Válassza ki a "Web App/API" típust
5. Adja meg a bejelentkezési URL-cím tetszőleges értékét, ezért\'ezt a mezőt fogjuk használni.
6. Kattintson a Létrehozás gombra.
7. Az alkalmazás létrehozása után lépjen a Tulajdonságok elemre, és\> állítsa be az alkalmazást több-bérlős értékre, majd kattintson a Save (Mentés) gombra.

Kattintson a Mentés gombra. Az utolsó lépés az alkalmazás AZONOSÍTÓjának beolvasása a regisztrált alkalmazáshoz, és beillesztés a test Drive (tesztelési meghajtó) mezőbe.

![Azure AD-alkalmazás AZONOSÍTÓjának részletei](./media/azure-resource-manager-test-drive/subdetails7.png)

Mivel az alkalmazást az előfizetésre való központi telepítésre használjuk, az alkalmazást közreműködőként kell hozzáadni az előfizetéshez. Az alábbi utasítások a következők:

1. Navigáljon az előfizetések panelre, és válassza ki a megfelelő előfizetést, amelyet csak a tesztelési meghajtóhoz használ.
1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.
1. Kattintson a **szerepkör-hozzárendelések** fülre.  ![Új Access Control rendszerbiztonsági tag hozzáadása](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Kattintson a **szerepkör-hozzárendelés hozzáadása**lehetőségre.
1. Adja meg a szerepkört **közreműködőként**.
1. Írja be az Azure AD-alkalmazás nevét, és válassza ki azt az alkalmazást, amelyhez hozzá szeretné rendelni a szerepkört.
    ![Engedélyek hozzáadása](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Kattintson a **Save** (Mentés) gombra.

**Azure ad alkalmazás Key –** *kötelező* a végső mező egy hitelesítési kulcs létrehozása. A kulcsok területen adja meg a kulcs leírását, állítsa be az időtartamot, hogy soha ne járjon le, majd válassza a mentés lehetőséget. **Fontos** elkerülni, hogy a rendszer ne jelentsen egy lejárt kulcsot, így a tesztelési meghajtót éles környezetben fogja megszüntetni. Másolja ezt az értéket, és illessze be a szükséges tesztvezetés mezőjébe.

![Megjeleníti az Azure AD-alkalmazás kulcsait](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>További lépések

Most, hogy kitöltötte az összes tesztelési meghajtó mezőjét, ugorjon át, és **tegye közzé újból** az ajánlatot. Ha a tesztvezetés megfelelt a minősítésnek, az ajánlat **előzetes** verziójában alaposan tesztelni kell a felhasználói élményt. Indítsa el a tesztelési meghajtót a felhasználói felületen, majd nyissa meg az Azure-előfizetését a Azure Portal belül, és ellenőrizze, hogy a tesztelési meghajtók teljes mértékben telepítve vannak-e.

![Azure Portal](./media/azure-resource-manager-test-drive/subdetails9.png)

Fontos megjegyezni, hogy nem törli az ügyfelek számára kiépített tesztvezetés-példányokat, így a test Drive szolgáltatás automatikusan törli ezeket az erőforráscsoportokat, miután az ügyfél befejezte azt.

Ha már elégedett az előzetes verzió ajánlatával, most itt az ideje, hogy **élj**! A Microsoft végső felülvizsgálati folyamata az ajánlat közzétételét követően, a teljes végpontok közötti élmény ellenőrzéséhez. Ha valamilyen okból elutasítják az ajánlatot, elküldünk egy értesítést az ajánlat mérnöki kapcsolattartójának, amely elmagyarázza, mit kell megjavítania.

Ha további kérdései vannak, hibaelhárítási tanácsokat talál, vagy ha szeretné, hogy a tesztvezetés sikeres legyen, tekintse meg a [Gyakori kérdések, hibaelhárítás és & ajánlott eljárásokat](./marketing-and-best-practices.md).
