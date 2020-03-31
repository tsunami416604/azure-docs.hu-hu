---
title: Azure Resource Manager tesztmeghajtó | Azure Piactér
description: Marketplace-tesztmeghajtó létrehozása az Azure Resource Manager használatával
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6125aa010d8676518b84f866343b01f95246160f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275934"
---
# <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager – Tesztverzió

Ez a cikk azon közzétevők számára készült, akik ajánlattal rendelkeznek az Azure Piactéren, vagy akik az AppSource-on vannak, de csak Azure-erőforrásokkal szeretnének tesztmeghajtót építeni.

Az Azure Resource Manager (Resource Manager) sablon az Azure-erőforrások kódolt tárolója, amelyet úgy tervez, hogy a legjobban képviselje a megoldást. Ha nem ismeri az Erőforrás-kezelő sablonjait, olvassa el az [Erőforrás-kezelő sablonjainak megértését](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) és [az Erőforrás-kezelő sablonjainak szerkesztését,](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) és győződjön meg arról, hogy tudja, hogyan hozhat létre és tesztelhet saját sablonokat.

A Test Drive azt teszi, hogy a megadott Erőforrás-kezelő sablont veszi igénybe, és az adott Erőforrás-kezelő sablonból szükséges összes erőforrást egy erőforráscsoportba telepíti.

Ha úgy dönt, hogy létrehoz egy Azure Resource Manager tesztmeghajtót, a követelmények a következők:

- A Test Drive Resource Manager-sablon létrehozása, tesztelése és feltöltése.
- A tesztvezetés engedélyezéséhez konfigurálja az összes szükséges metaadatot és beállítást.
- Tegye közzé újra az ajánlatot úgy, hogy a Test Drive engedélyezve van.

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>Azure Resource Manager-tesztmeghajtó létrehozása

Az Azure Resource Manager tesztmeghajtó létrehozásának folyamata:

1. Tervezze meg, hogy mit szeretne az ügyfelektől egy folyamatábrán.
1. Határozza meg, hogy milyen élményeket szeretne az ügyfelek számára.
1. A fenti definíciók alapján döntse el, hogy milyen darabokra és erőforrásokra van szükség az ügyfelek számára az ilyen élmény elvégzéséhez: például D365 példány, vagy egy adatbázissal rendelkező webhely.
1. Helyileg készítse el a tervet, és tesztelje az élményt.
1. Csomagolja be a felhasználói élményt egy ARM sablon telepítésében, és onnan:
    1. Adja meg, hogy az erőforrások mely részei bemeneti paraméterek;
    1. Milyen változók;
    1. Milyen kimeneteket kap nak az ügyfélélmény.
1. Közzététel, teszt, és élőben.

Az Azure Resource Manager testdrive létrehozásának legfontosabb része annak meghatározása, hogy milyen forgatókönyv(eke)t szeretne az ügyfeleknek megtapasztalni. Ön egy tűzfal termék, és azt szeretné, hogy demo, milyen jól kezeli script injekció támadások? Ön tárolási termék, és szeretné demo, hogy milyen gyorsan és egyszerűen a megoldás tömöríti a fájlokat?

Győződjön meg róla, hogy elegendő időt töltenek annak értékelésével, hogy melyek a legjobb módja annak, hogy megmutassák a terméket. Különösen az összes szükséges erőforrás körül, amire szüksége van, mivel ez elég könnyebbé teszi az Erőforrás-kezelő sablon csomagolását.

A tűzfalpéldával való folytatáshoz az architektúra lehet, hogy szüksége van egy nyilvános IP-URL-re a szolgáltatásához, és egy másik nyilvános IP-URL-re a tűzfal által védett webhelyhez. Minden IP-cím egy virtuális gépen van telepítve, és egy hálózati biztonsági csoporttal + hálózati adapterrel van összekötve.

Miután megtervezte a kívánt erőforráscsomagot, most jön a Test Drive Resource Manager sablon írása és létrehozása.

## <a name="writing-test-drive-resource-manager-templates"></a>Test Drive Erőforrás-kezelő sablonok írása

A Test Drive teljesen automatizált módban futtatja a központi telepítéseket, és emiatt a Test Drive-sablonokra az alábbiakban ismertetett korlátozások vonatkoznak.

### <a name="parameters"></a>Paraméterek

A legtöbb sablon paraméterkészletével rendelkezik. A paraméterek erőforrásneveket, erőforrásméreteket (például tárfiókok vagy virtuálisgép-méretek típusai), felhasználóneveket és jelszavakat, DNS-neveket és így tovább határozzák meg. Amikor megoldásokat telepít az Azure Portal használatával, manuálisan feltöltheti ezeket a paramétereket, kiválaszthatja az elérhető DNS-neveket vagy tárfiókneveket, és így tovább.

![Paraméterek listája az Azure Resource Managerben](./media/azure-resource-manager-test-drive/param1.png)

A Test Drive azonban teljesen automatikus módban működik, emberi beavatkozás nélkül, így csak korlátozott számú paraméterkategóriát támogat. Ha a Test Drive Resource Manager sablon egyik paramétere nem tartozik a támogatott kategóriák egyikébe, akkor ezt a **paramétert változó vagy állandó értékre kell cserélnie.**

Bármilyen érvényes nevet használhat a paraméterekhez, a Test Drive metaadat-típus értékhasználatával ismeri fel a paraméterkategóriát. **Minden sablonparaméterhez meg kell adnia metaadat-típust,** ellenkező esetben a sablon nem felel meg az érvényesítésnek:

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

Azt is fontos megjegyezni, hogy **minden paraméter nem**\'kötelező , így ha\'nem szeretné használni, akkor nem kell.

### <a name="accepted-parameter-metadata-types"></a>Elfogadott paramétermetaadat-típusok

| Metaadat típusa   | Paraméter típusa  | Leírás     | Mintaértéke    |
|---|---|---|---|
| **baseuri között**     | sztring          | A központi telepítési csomag alap URI-ja| \//\<https:\.. \>blob.core.windows.net/\<\..\> |
| **Felhasználónév**    | sztring          | Új véletlenszerű felhasználónév.| admin68876      |
| **alaphelyzetbe állítása**    | biztonságos karakterlánc    | Új véletlenszerű jelszó | Lp!ACS\^2kh     |
| **munkamenet-azonosító**   | sztring          | Egyedi tesztvezetés-munkamenet-azonosító (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>baseuri között

A Test Drive inicializálja ezt a paramétert a központi telepítési csomag base Uri-jával, így ezzel a paraméterrel hozhat létre uri-t a csomagba foglalt bármely fájlból. **Base Uri**

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

A sablonon belül ezt a paramétert használhatja a Test Drive központi telepítési csomagból származó bármely fájl Uri-jának létrehozásához. Az alábbi példa bemutatja, hogyan lehet létrehozni egy Uri a csatolt sablon:

```json
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>felhasználónév

A Test Drive ezt a paramétert egy új véletlenszerű felhasználónévvel inicializálja:

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

Mintaértéke:

    admin68876

A megoldáshoz véletlenszerű vagy állandó felhasználóneveket is használhat.

#### <a name="password"></a>jelszó

A Test Drive egy új véletlenszerű jelszóval inicializálja ezt a paramétert:

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

Mintaértéke:

    Lp!ACS^2kh

A megoldáshoz véletlenszerű vagy állandó jelszavakat is használhat.

#### <a name="session-id"></a>munkamenet azonosítója

A Test Drive inicializálja ezt a paramétert egy egyedi GUID azonosítóval, amely a Test Drive munkamenet-azonosítóját jelöli:

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

Mintaértéke:

    b8c8693e-5673-449c-badd-257a405a6dee

Ezzel a paraméterrel egyedileg azonosíthatja a Test Drive munkamenetet, ha szükséges.

### <a name="unique-names"></a>Egyedi nevek

Egyes Azure-erőforrások, például a tárfiókok vagy a DNS-nevek globálisan egyedi neveket igényelnek.

Ez azt jelenti, hogy minden alkalommal, amikor a Test Drive telepíti az Erőforrás-kezelő sablont, létrehoz egy **új erőforráscsoportot, amelynek egyedi neve** van az összes erőforrásához.\' Ezért véletlenszerű egyedi értékek létrehozásához az erőforráscsoport-azonosítókon a változónevekkel összefonva [lévő egyedi karakterláncfüggvényt](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) kell használnia:

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Győződjön meg arról, hogy összefűzi a paraméter/változó karakterláncok (\'contosovm\') egy egyedi karakterlánckimenet (\'resourceGroup(.id\'), mert ez garantálja az egyediségét és megbízhatóságát minden változó.

A legtöbb erőforrásnév például nem kezdheti el számjeggyel, de az egyedi karakterláncfüggvény visszaadhat egy karakterláncot, amely számjeggyel kezdődik. Így ha nyers egyedi karakterlánc-kimenetet használ, a központi telepítések sikertelenek lesznek. 

Az erőforrás-elnevezési szabályokról és korlátozásokról ebben a [cikkben](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)talál további információt.

### <a name="deployment-location"></a>Telepítési hely

A Test Drive-ot elérhetővé teheti a különböző Azure-régiókban. Az ötlet az, hogy a felhasználó, hogy vegye ki a legközelebbi régióban, hogy a fenevad felhasználói élményt.

Amikor a Test Drive létrehoz egy példányt a lab, mindig létrehoz egy erőforráscsoportot a régióban egy felhasználó által kiválasztott, majd végrehajtja a központi telepítési sablon ebben a csoportkörnyezetben. Így a sablonnak ki kell választania a központi telepítési helyet az erőforráscsoportból:

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

Ezután használja ezt a helyet minden erőforráshoz egy adott lab példányhoz:

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

Győződjön meg arról, hogy az előfizetés e kiválasztott régiókban telepítheti az összes erőforrást. Azt is meg kell győződnie arról, hogy a virtuális géplemezképek elérhetők az összes régióban, engedélyezni fog, különben a központi telepítési sablon nem fog működni egyes régiókban.

### <a name="outputs"></a>Kimenetek

Általában az Erőforrás-kezelő sablonokkal kimenet nélkül is üzembe helyezheti. Ennek az az oka, hogy ismeri a sablonparaméterek feltöltéséhez használt összes értéket, és bármikor manuálisan ellenőrizheti bármely erőforrás tulajdonságait.

A Test Drive Resource Manager-sablonok esetében azonban fontos,\'hogy visszatérjen a Test Drive-ra az összes olyan információ, amely a laborhoz való hozzáféréshez szükséges (webhely URI-k, virtuálisgép-állomásnevek, felhasználónevek és jelszavak). Győződjön meg arról, hogy az összes kimeneti név olvasható, mert ezek a változók jelennek meg az ügyfélnek.

A sablonkimenetekkel kapcsolatban nincsenek korlátozások. Ne feledje, hogy a Test Drive az összes kimeneti értéket **karakterláncokká alakítja,** így ha objektumot küld a kimenetre, a felhasználó látni fogja a JSON-karakterláncot.

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

Még egy dolog, amit figyelembe kell vennie az előfizetési és szolgáltatási korlátok. Ha például legfeljebb tíz négymagos virtuális gépet szeretne üzembe helyezni, győződjön meg arról, hogy a Laborhoz használt előfizetés 40 mag használatát teszi lehetővé.

Az Azure-előfizetési és szolgáltatáskorlátokról ebben [a cikkben](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)talál további információt. Mivel egyszerre több tesztmeghajtó is elvállalható, ellenőrizze, \# hogy az előfizetés képes-e kezelni a magok és az egyidejű tesztmeghajtók teljes számának szorzatát.

### <a name="what-to-upload"></a>Mit kell feltölteni?

A Test Drive Resource Manager sablonzip fájlként töltődik fel, amely különböző telepítési összetevőket tartalmazhat, de egy **main-template.json**nevű fájllal kell rendelkeznie. Ez a fájl az Azure Resource Manager telepítési sablon, és a Test Drive egy labor példányosítására használja.

Ha a fájlon kívül további erőforrásokkal is rendelkezik, hivatkozhat rá külső erőforrásként a sablonon belül, vagy felveheti az erőforrást a zip fájlba.

A közzétételi tanúsítvány során a Test Drive kicsomagolja a központi telepítési csomagot, és annak tartalmát egy belső tesztmeghajtó blobtárolóba helyezi. A tárolószerkezet a központi telepítési csomag szerkezetét tükrözi:

| csomag.zip                       | Tesztmeghajtó blobtárolója         |
|---|---|
| fő-template.json                | \//\<https:\... \>.blob.core.windows.net/..\<\. \>/main-template.json  |
| sablonok/solution.json           | \//\<https:\... \>.blob.core.windows.net/..\<\. \>/templates/solution.json |
| parancsfájlok/warmup.ps1                | \//\<https:\... \>.blob.core.windows.net/..\<\. \>/scripts/warmup.ps1  |


Ennek a blob konténernek az Uri-t hívjuk. A labor minden felülvizsgálata saját blob tároló, és ezért a labor minden felülvizsgálata saját Base Uri. A Test Drive a sablon paramétereken keresztül átadhatja a kibontott központi telepítési csomag alapuri-ját a sablonba.

## <a name="transforming-template-examples-for-test-drive"></a>Sablonpéldák átalakítása a tesztvezetéshez

Az erőforrások architektúrájának Test Drive Resource Manager sablonná alakítása ijesztő lehet. Annak érdekében, hogy megkönnyítsük ezt a folyamatot, példákat készítettünk\'arra, hogyan lehet a legjobban átalakítani a jelenlegi telepítési [sablonokat itt.](./transforming-examples-for-test-drive.md)

## <a name="how-to-publish-a-test-drive"></a>Tesztvezetés közzététele

Most, hogy már megvan a Test Drive beépített, ez a szakasz végigvezeti az egyes mezők szükségesek ahhoz, hogy sikeresen tegye közzé a Test Drive.

![Tesztvezetés engedélyezése a felhasználói felületen](./media/azure-resource-manager-test-drive/howtopub1.png)

Az első és legfontosabb mező az, hogy váltani, hogy szeretné Test Drive engedélyezve van az ajánlatot, vagy sem. Ha az Igen lehetőséget **választja,** az űrlap többi része az összes szükséges mezővel együtt megjelenik a kitöltandó mezők kel. Ha a Nem lehetőséget választja, az űrlap letiltásra **kerül,** és ha újra közzéteszi a Tesztmeghajtót, a tesztmeghajtó törlődik az éles környezetből.

Megjegyzés: Ha a felhasználók aktívan használják a tesztmeghajtókat, akkor ezek a tesztmeghajtók a munkamenet lejártáig továbbra is futni fognak.

### <a name="details"></a>Részletek

A következő kitöltandó szakasz a Test Drive ajánlat részletei.

![Test Drive részletes információk](./media/azure-resource-manager-test-drive/howtopub2.png)

**Leírás -** *Kötelező* Ez az, ahol írni a fő leírást arról, hogy mi van a test drive. Az ügyfél jön ide, hogy olvassa el, milyen forgatókönyveket a Test Drive lesz, amely a termékről. 

**Használati utasítás –** *Kötelező:* Ez a Tesztvezetés-élmény részletes forgatókönyve. Az ügyfél megnyitja ezt, és végigsétálhat pontosan azon, amit szeretne, hogy tegyenek a Tesztvezetés során. Fontos, hogy ez a tartalom könnyen érthető és követhető legyen! (.pdf fájlnak kell lennie)

**Test Drive Demo Video -** *Ajánlott* Hasonló a felhasználói kézikönyv, a legjobb, ha egy video tutorial a Test Drive tapasztalat. Az ügyfél fogja nézni ezt a korábbi vagy során test drive, és végigjárni, hogy pontosan mit szeretne, hogy nem az egész Test Drive. Fontos, hogy ez a tartalom könnyen érthető és követhető legyen!

- **Név** - A videó címe
- **Link** - Kell egy beágyazott URL-t a cső vagy videó. Példa arra, hogyan lehet a beágyazott URL alatt van:
- **Thumbnail** - Jó minőségű képnek (533x324) kell lennie. Javasoljuk, hogy itt képernyőképet készít a Test Drive-élmény egy részéről.

Az alábbiakban azt olvashatja, hogy ezek a mezők hogyan jelennek meg az ügyfél számára a Test Drive-élmény során.

![A Test Drive mezők helye a Marketplace-ajánlatban](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Műszaki konfiguráció

A következő kitöltendő szakasz az, ahol feltölti a Test Drive Resource Manager sablont, és meghatározza, hogy a Tesztmeghajtó-példányok hogyan működnek.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**Példányok –** *Ez* az a hely, ahol konfigurálhatja, hogy hány példányt szeretne, milyen régióban(ok) és milyen gyorsan szerezheti be az ügyfelek a Tesztmeghajtót.

- **Példányok** – A Select régiók, ahol kiválaszthatja, ahol a Test Drive Resource Manager sablon telepítve van. Javasoljuk, hogy csak válasszon egy régiót, ahol a legtöbb elvárják az ügyfelek találhatók.
- **Gyors** – A már üzembe helyezett és hozzáférésre váró tesztmeghajtó-példányok száma. Az ügyfelek azonnal elérhetik ezt a tesztmeghajtót, és nem kell megvárniuk a központi telepítést. A kompromisszum az, hogy ezek a példányok mindig fut nak az Azure-előfizetés, így nagyobb uptime költség merülnek fel. Erősen ajánlott, hogy **legalább egy hot példány,** mivel a legtöbb az ügyfelek nem szeretné megvárni a teljes üzembe helyezés befejezéséhez, és így van egy lemorzsolódás az ügyfél-használat.
- **Meleg** – A tesztmeghajtó-példányok száma régiónként, amelyek üzembe helyeztek, majd a virtuális gép leállt, és az Azure storage-ban tárolt. A meleg példányok várakozási ideje lassabb, mint a forró példányok, de a tárolás uptime költsége is olcsóbb.
- **Hideg** – A tesztmeghajtó-példányok száma régiónként, amely esetleg telepíthető. A hideg példányok megkövetelik, hogy a teljes Test Drive Resource Manager-sablon a Tesztmeghajtót kérő ügyfél központi telepítésekor menjen keresztül, így lassabb, mint a Meleg vagy meleg példányok. Azonban a kompromisszum az, hogy csak akkor kell fizetni e közben a Test Drive.

Jelenleg kiszámítja a lehetséges párhuzamos tesztmeghajtók teljes számát, és ellenőrzi, hogy az előfizetésre vonatkozó kvótakorlát képes-e kezelni ezt az egyidejű összeget:

**(Kijelölt régiók száma x Forró példányok száma) + (Kijelölt régiók száma x Meleg példányok) + (Kijelölt régiók száma x Hideg példányok)**

**Tesztvezetés időtartama (óra) –** A tesztvezetés aktív állapotának \# időtartamára vonatkozó *időtartam,* néhány óra alatt. A tesztvezetés automatikusan leáll ezen időszak lejárta után.

**Test Drive Resource Manager sablon –** *Az* Erőforrás-kezelő sablon feltöltése itt. Ez az a fájl, amelyet a fenti szakaszban készített. Nevezze el a fő sablonfájlt: "main-template.json", és győződjön meg arról, hogy az Erőforrás-kezelő sablon tartalmazza a szükséges kulcsváltozók kimeneti paramétereit. (.zip fájlnak kell lennie)

**Hozzáférési információk –** *Kötelező,* miután az ügyfél megkapja a Tesztmeghajtót, a hozzáférési információk megjelennek számukra. Ezek az utasítások a Test Drive Resource Manager sablon hasznos kimeneti paramétereinek megosztására szolgálnak. A kimeneti paraméterek felvételéhez használjon dupla göndör szögletes zárójeleket (például **{{outputname}}**), és a rendszer helyesen szúrja be őket a helyre. (Html karakterlánc formázás ajánlott itt jelenik meg az előlapon).

### <a name="test-drive-deployment-subscription-details"></a>A Test Drive-alapú üzembe helyezési előfizetés részletei

Az utolsó kitöltendő szakasz az Azure-előfizetés és az Azure Active Directory (AD) automatikus üzembe helyezésének lehetővé teszi.

![A Test Drive üzembe helyezésének előfizetésének részletei](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure-előfizetés-azonosító –** *Ez hozzáférést* biztosít az Azure-szolgáltatásokhoz és az Azure Portalhoz. Az előfizetés, ahol az erőforrás-használat jelentik, és a szolgáltatások számlázása. Ha még nem rendelkezik **külön** Azure-előfizetéssel csak tesztmeghajtókhoz, folytassa, és készítsen egyet. Az Azure-előfizetés-azonosítókat az Azure Portalra való bejelentkezéssel és a bal oldali menü előfizetések parancsára való navigálással találhatja meg. (Példa: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Azure-előfizetések](./media/azure-resource-manager-test-drive/subdetails2.png)

**Azure AD-bérlői azonosító –** *szükséges,* ha rendelkezik egy bérlői azonosító\> már elérhető megtalálja az alábbiakban a Tulajdonságok - Címtár-azonosító.

![Az Azure Active Directory tulajdonságai](./media/azure-resource-manager-test-drive/subdetails3.png)

Ellenkező esetben hozzon létre egy új bérlőt az Azure Active Directoryban.

![Az Azure Active Directory-bérlők listája](./media/azure-resource-manager-test-drive/subdetails4.png)

![Az Azure AD-bérlő szervezetének, tartományának és országának/régiójának meghatározása](./media/azure-resource-manager-test-drive/subdetails5.png)

![A kijelölés megerősítése](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure AD App ID –** *Szükséges* Következő lépés egy új alkalmazás létrehozása és regisztrálása. Ezt az alkalmazást arra fogjuk használni, hogy műveleteket hajtsunk végre a Test Drive-példányon.

1. Keresse meg az újonnan létrehozott könyvtárat vagy a már meglévő könyvtárat, és válassza az Azure Active directory a szűrőablaktáblán.
2. Keressen rá az "Alkalmazásregisztrációk" kifejezésre, és kattintson a "Hozzáadás" gombra
3. Adja meg az alkalmazás nevét.
4. Válassza ki a típusa a "Web app / API"
5. Adjon meg bármilyen értéket a bejelentkezési URL-címben,\'akkor nem fogjuk használni ezt a mezőt.
6. Kattintson a Létrehozás gombra.
7. Az alkalmazás létrehozása után nyissa meg\> a Tulajdonságok – Állítsa be az alkalmazást több-bérlősként, és nyomja le a Mentés parancsot.

Kattintson a Mentés gombra. Az utolsó lépés az, hogy megragad az alkalmazás azonosítója ehhez a regisztrált alkalmazáshoz, és illessze be a Test Drive mezőbe itt.

![Az Azure AD-alkalmazásazonosító részletei](./media/azure-resource-manager-test-drive/subdetails7.png)

Tekintettel arra, hogy az alkalmazást az előfizetés üzembe helyezéséhez használjuk, hozzá kell adnunk az alkalmazást az előfizetés közreműködőjeként. Ezekre vonatkozó utasítások a következők:

1. Keresse meg az Előfizetések panelt, és válassza ki a tesztvezetéshez használt megfelelő előfizetést.
1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.
1. Kattintson a **Szerepkör-hozzárendelések** fülre.  ![Új hozzáférés-vezérlési tag hozzáadása](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Kattintson **a Szerepkör-hozzárendelés hozzáadása gombra.**
1. Állítsa be a szerepkört **közreműködőként.**
1. Írja be az Azure AD-alkalmazás nevét, és válassza ki a szerepkör hozzárendeléséhez az alkalmazást.
    ![Engedélyek hozzáadása](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Kattintson a **Mentés** gombra.

**Azure AD alkalmazáskulcs –** *Kötelező A* végső mező egy hitelesítési kulcs létrehozása. A billentyűk alatt adjon hozzá egy kulcsleírást, állítsa úgy az időtartamot, hogy soha ne járjon le, majd válassza a mentés lehetőséget. **Fontos,** hogy ne legyen lejárt kulcs, amely megszakítja a tesztmeghajtót éles környezetben. Másolja másra ezt az értéket, és illessze be a szükséges Tesztmeghajtó mezőbe.

![Az Azure AD-alkalmazás kulcsait jeleníti meg](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>További lépések

Most, hogy az összes Tesztmeghajtó mező ki lett töltve, menjen át, és **tegye közzé újra** az ajánlatot. Miután a Test Drive megfelelt a minősítésen, alaposan tesztelnie kell az ügyfélélményt az ajánlat **előzetes verziójában.** Indítsa el a tesztmeghajtót a felhasználói felületen, majd nyissa meg az Azure-előfizetést az Azure Portalon belül, és ellenőrizze, hogy a tesztmeghajtók teljes körűen telepítve vannak-e.

![Azure portál](./media/azure-resource-manager-test-drive/subdetails9.png)

Fontos megjegyezni, hogy nem törli a Test Drive-példányokat, mivel azok ki vannak építve az ügyfelek számára, így a Test Drive szolgáltatás automatikusan törli ezeket az erőforráscsoportokat, miután az ügyfél befejezte azt.

Ha már jól érzi magát az Előzetes ajánlattal, most itt az ideje, hogy **élőben induljon!** Van egy végső felülvizsgálati folyamat a Microsoft, miután az ajánlat már közzétették, hogy ellenőrizze a teljes végponttól végpontig tapasztalat. Ha valamilyen okból az ajánlatot elutasítják, értesítést küldünk az ajánlat műszaki kapcsolattartójának, amelyben elmagyarázza, hogy mit kell javítani.

Ha további kérdései vannak, hibaelhárítási tanácsokat keres, vagy szeretné sikeresebbé tenni a Tesztmeghajtót, olvassa el a [GYIK, hibaelhárítás & gyakorlati tanácsok című témakört.](./marketing-and-best-practices.md)
