# <a name="how-to-use-the-azure-command-line-tools-for-mac-and-linux"></a>A Macre és Linuxra készült Azure parancssori eszközök használata
Ez az útmutató leírja, hogyan kell használni a Macre és Linuxra készült Azure parancssori eszközöket az Azure szolgáltatásainak létrehozásához és kezeléséhez. A forgatókönyvek a következőket tartalmazzák: **az eszközök telepítése**, **a közzétételi beállítások importálása**, **az Azure Websites létrehozása és kezelése** és **az Azure Virtual Machines létrehozása és kezelése**. Az átfogó referenciadokumentációhoz tekintse meg [A Macre és Linuxra készült Azure parancssori eszközök használata][reference-docs] című témakört. 

## <a name="table-of-contents"></a>Tartalomjegyzék
* [A Macre és Linuxra készült Azure parancssori eszközök bemutatása](#Overview)
* [A Macre és Linuxra készült Azure parancssori eszközök telepítése](#Download)
* [Azure-fiók létrehozása](#CreateAccount)
* [Közzétételi beállítások letöltése és importálása](#Account)
* [Azure-webhely létrehozása és kezelése](#WebSites)
* [Azure-beli virtuális gép létrehozása és kezelése](#VMs)

<h2><a id="Overview"></a>A Macre és Linuxra készült Azure parancssori eszközök bemutatása</h2>

Az Azure Macre és Linuxra készült parancssori eszközeinek gyűjteménye Azure-szolgáltatások üzembe helyezésére és kezelésére szolgál.

A támogatott feladatok a következők:

* Közzétételi beállítások importálása
* Azure Websites létrehozása és kezelése
* Azure Virtual Machines létrehozása és kezelése

A támogatott parancsok teljes listájának megjelenítéséhez az eszközök telepítése után írja be a parancssorba a következőt: `azure -help`, vagy tekintse meg a [referenciadokumentációt][reference-docs].

<h2><a id="Download">A Macre és Linuxra készült Azure parancssori eszközök telepítése</a></h2>

Az alábbi lista a parancssori eszközök az operációs rendszertől függő telepítési információit tartalmazza:

* **Mac**: Töltse le az [Azure SDK Installert][mac-installer]. Nyissa meg a letöltött .pkg fájlt, és végezze el a telepítési lépéseket.
* **Linux**: Telepítse a [Node.js][nodejs-org] legfrissebb verzióját (lásd még: [A Node.js telepítése a csomagkezelő segítségével][install-node-linux]), majd futtassa a következő parancsot:
  
        npm install azure-cli -g
  
    **Megjegyzés**: A parancs futtatásához szükség lehet emelt szintű jogosultságokra:
  
        sudo npm install azure-cli -g
* **Windows**: Futtassa a Windows Installert (.msi fájl), amely innen érhető el: [Azure parancssori eszközök][windows-installer].

A telepítés teszteléséhez írja be az `azure` parancsot a parancssorba. Ha a telepítés sikeres, megjelenik az elérhető `azure`-parancsok listája.

<h2><a id="CreateAccount"></a>Azure-fiók létrehozása</h2>

A Macre és Linuxra készült Azure parancssori eszközök használatához Azure-fiókra van szükség.

Nyisson meg egy böngészőt, majd keresse fel a következő helyet: [http://www.windowsazure.com][windowsazuredotcom]. Kattintson az **ingyenes fiók** elemre a jobb felső sarokban.

![Azure-webhely][Azure Web Site]

Kövesse a fiók létrehozására vonatkozó utasításokat.

<h2><a id="Account"></a>Közzétételi beállítások letöltése és importálása</h2>

Első lépésként a közzétételi beállításait kell letöltenie, majd importálnia. Ez lehetővé teszi az Azure-szolgáltatások létrehozásához és kezeléséhez szükséges eszközök használatát. A közzétételi beállítások letöltéséhez használja az `account download` parancsot:

    azure account download

Ekkor megnyílik az alapértelmezett böngésző, amellyel be kell jelentkeznie a felügyeleti portálra. Bejelentkezés után letöltődik a `.publishsettings` fájl. Jegyezze fel a letöltött fájl elérési útját.

Következő lépésként importálja a `.publishsettings` fájlt a következő parancs futtatásával. A `{path to .publishsettings file}` helyett adja meg a `.publishsettings` fájl elérési útját:

    azure account import {path to .publishsettings file}

Az <code>import</code> parancs által tárolt összes információt eltávolíthatja az <code>account clear</code> parancs használatával:

    azure account clear

Az `account`-parancsok beállításainak megtekintéséhez használja a `-help` paramétert:

    azure account -help

A közzétételi beállítások importálása után biztonsági okokból javasolt a `.publishsettings` fájl törlése.

> [!NOTE]
> A közzétételi beállítások importálásakor az Azure-előfizetéshez való hozzáféréshez szükséges hitelesítő adatok a `user` mappába kerülnek. A `user` mappát az operációs rendszer védi. Javasoljuk azonban, hogy további lépéseket tegyen a `user` mappa titkosítására. Ezt a következő módokon teheti meg:    
> 
> * Windows: Módosítsa a mappatulajdonságokat, vagy használjon BitLocker meghajtótitkosítást.
> * Mac: Kapcsolja be a FileVault szolgáltatást a mappához.
> * Ubuntu: Használjon titkosított kezdőmappát. Más Linux-disztribúciók is nyújtanak hasonló szolgáltatásokat.
> 
> 

Most már készen áll arra, hogy megkezdje az Azure Websites és az Azure Virtual Machines létrehozását és kezelését.  

<h2><a id="WebSites"></a>Azure-webhely létrehozása és kezelése</h2>

### <a name="create-a-website"></a>Webhely létrehozása
Azure-webhely létrehozásához először hozzon létre egy üres mappát `MySite` névvel, majd nyissa meg.

Ezután futtassa a következő parancsot:

    azure site create MySite --git

A parancs kimenete tartalmazza az újonnan létrehozott webhely alapértelmezett URL-címét. A `--git` paraméter lehetővé teszi a git használatát a webhely közzétételéhez oly módon, hogy git-adattárakat hoz létre mind a helyi alkalmazáskönyvtárban, mind a webhely adatközpontjában. Vegye figyelembe, hogy ha a helyi mappa már git-adattárként működik, a parancs új távelérést ad hozzá a meglévő adattárhoz, amely a webhely adatközpontjában található adattárra mutat.

Az `azure site create` parancsot a következő paraméterek bármelyikével futtathatja:

* `--location [location name]`. Ezzel a paraméterrel megadhatja a helyét annak az adatközpontnak, amelyben a webhelyét létrehozza (pl.: „USA nyugati régiója”). Ha kihagyja ezt a beállítást, a hely kiválasztásához promted fogja.
* `--hostname [custom host name]`. Ezzel a paraméterrel a webhelyének egyéni állomásnevet adhat.

Ezután a webhely könyvtárához hozzáadhatja a tartalmat. Használja a normál git-folyamatot (`git add`, `git commit`) a tartalom véglegesítéséhez. Használja a következő git-parancsot a webhely tartalmának az Azure-ba történő küldéséhez: 

    git push azure master

### <a name="set-up-publishing-from-github"></a>GitHubról történő közzététel beállítása
GitHub-adattárról történő folyamatos közzététel beállításához használja a `--GitHub` paramétert a webhely létrehozásakor:

    auzre site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

Ha rendelkezik a GitHub-adattár helyi klónjával vagy egy GitHub-adattárra mutató távoli hivatkozással, ez a parancs automatikusan közzéteszi a GitHub-adattárban lévő kódot a webhelyen. Onnantól kezdve a GitHubra leküldött bármilyen módosítás a webhelyen is megjelenik.

Amikor beállítja a GitHubról történő közzétételt, az alapértelmezett ág a „master” ág. Ha másik ágat szeretne megadni, a helyi adattárból futtassa a következő parancsot:

    azure site repository <branch name>

### <a name="configure-app-settings"></a>Alkalmazásbeállítások konfigurálása
Az alkalmazásbeállítások kulcs-érték párok, amelyeket az alkalmazás a futásideje alatt tud elérni. Az Azure-webhelyhez történő beállításkor az alkalmazásbeállítások felülbírálják az ugyanazon kulcshoz tartozó beállításokat, amelyek a webhely Web.config fájljában találhatók. Node.js- és PHP-alkalmazások esetén az alkalmazásbeállítások környezeti változókként érhetők el. A következő példa egy kulcs-érték pár beállítását mutatja be:

    azure site config add <key>=<value> 

Az összes kulcs-érték pár megjelenítéséhez használja a következőt:

    azure site config list 

Vagy, ha ismeri a kulcsot, és látni szeretné az értékét, használhatja a következőt:

    azure site config get <key> 

Ha módosítani szeretné egy meglévő kulcs értékét, akkor törölnie kell a meglévő kulcsot, majd újra hozzá kell adnia. A törlési parancs a következő:

    azure site config clear <key> 

### <a name="list-and-show-sites"></a>Webhelyek listázása és megjelenítése
A webhelyek kilistázásához használja az alábbi parancsot:

    azure site list

Ha részletes információt szeretne kapni egy webhelyről, használja a `site show` parancsot. Az alábbi példában a `MySite` részletei láthatók:

    azure site show MySite

### <a name="stop-start-or-restart-a-site"></a>Hely leállítása, indítása vagy újraindítása
Leállíthat, elindíthat vagy újraindíthat egy webhelyet a `site stop`, `site start` vagy `site restart` paranccsal:

    azure site stop MySite
    azure site start MySite
    azure site restart MySite

### <a name="delete-a-site"></a>Hely törlése
Végezetül le is törölhet egy webhelyet a `site delete` paranccsal:

    azure site delete MySite

Vegye figyelembe, hogy ha a fenti parancsok bármelyikét abból a mappából futtatja, ahol a `site create` parancsot futtatta, akkor nem szükséges megadnia a webhely nevét (`MySite`) utolsó paraméterként.

A `site`-parancsok teljes listájának megtekintéséhez használja a `-help` paramétert:

    azure site -help 

<h2><a id="VMs"></a>Azure-beli virtuális gép létrehozása és kezelése</h2>

Egy Ön által megadott vagy a rendszerkép-katalógusban elérhető virtuális gép rendszerképéből (.vhd fájl) létrejön egy Azure-beli virtuális gép. Az elérhető rendszerképek megtekintéséhez használja a `vm image list` parancsot:

    azure vm image list

Az elérhető rendszerképekből a `vm create` paranccsal létesíthet és indíthat el virtuális gépet. Az alábbi példa szemlélteti, hogyan lehet Linux alatt virtuális gépet létrehozni (melynek neve: `myVM`) a rendszerkép-katalógusban található egyik rendszerképből (CentOS 6.2). A virtuális géphez tartozó gyökérszintű felhasználónév és jelszó: `myusername`, illetve `Mypassw0rd`. (Vegye figyelembe, hogy a `--location` paraméter határozza meg azt az adatközpontot, amelyben létrejön a virtuális gép. Ha kihagyja a `--location` paramétert, a rendszer felkéri a hely kiválasztására.)

    azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd myusername --location "West US"

Érdemes lehet megadni a `--ssh` jelölőt (Linux) vagy az `--rdp` jelölőt (Windows) a `vm create` esetén az újonnan létrehozott virtuális géppel való távoli kapcsolatok engedélyezéséhez.

Ha inkább egyéni rendszerképből szeretne virtuális gépet kiépíteni, létrehozhat rendszerképet egy .vhd fájlból a `vm image create` parancs használatával, majd a `vm create` paranccsal kiépítheti a virtuális gépet. Az alábbi példa bemutatja, hogyan hozhat létre egy linuxos rendszerképet (melynek neve: `myImage`) egy helyi .vhd fájlból. (A `--location` paraméter határozza meg azt az adatközpontot, amelyben a rendszerkép megtalálható.)

    azure vm image create myImage /path/to/myImage.vhd --os linux --location "West US"

Helyi .vhd fájl helyett az Azure Blob Storage-ban tárolt .vhd fájlból is létrehozhat rendszerképet. Ezt a `blob-url` paraméterrel teheti meg:

    azure vm image create myImage --blob-url <url to .vhd in Blob Storage> --os linux

Miután létrehozta a rendszerképet, kiépíthet belőle virtuális gépet a `vm create` használatával. Az alábbi parancs létrehoz egy `myVM` elnevezésű virtuális gépet a fent létrehozott rendszerképből (`myImage`).

    azure vm create myVM myImage myusername --location "West US"

Miután kiépítette a virtuális gépet, érdemes lehet végpontokat létrehozni, például hogy távolról is hozzáférhessen a virtuális géphez. Az alábbi példa a `vm create endpoint` parancsot használja a 22-es külső port és a 22-es helyi port megnyitására a `myVM` esetén:

    azure vm endpoint create myVM 22 22

A `vm show` paranccsal részletes információkat jeleníthet meg a virtuális gépről (beleértve az IP-címet, a DNS-nevet és a végpont-információkat):

    azure vm show myVM

A virtuális gép kikapcsolásához, elindításához vagy újraindításához használja az alábbi parancsok egyikét:

    azure vm shutdown myVM
    azure vm start myVM
    azure vm restart myVM

Végezetül a virtuális gép törléséhez használja a `vm delete` parancsot:

    azure vm delete myVM

A virtuális gépek létrehozásához és kezeléséhez használt parancsok teljes listájának megtekintéséhez használja a `-h` paramétert:

    azure vm -h

<!-- IMAGES -->
[Azure Web Site]: ./media/crossplat-cmd-tools/freetrial.png

<!-- LINKS -->
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/?LinkID=275464
[reference-docs]: http://go.microsoft.com/fwlink/?LinkId=252246
[windowsazuredotcom]: http://www.windowsazure.com



<!--HONumber=Jan17_HO5-->


