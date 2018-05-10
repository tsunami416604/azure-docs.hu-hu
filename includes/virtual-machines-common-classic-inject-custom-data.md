


Ez a cikk ismerteti, hogyan:

* Szúrjon be egy Azure virtuális gép (VM) adatokat, amikor folyamatban van.
* Beolvasni a Windows és Linux.
* Egyes rendszerek különleges elérhető eszközök segítségével észleli és automatikusan kezelni az egyéni adatokat.

> [!NOTE]
> Ez a cikk ismerteti, hogyan egyéni adatok beszúrhatja egy virtuális gép létrehozása a klasszikus üzembe helyezési modell használatával. Az Azure erőforrás-kezelési API használatával, olvassa el [a példa sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).
> 
> 

## <a name="injecting-custom-data-into-your-azure-virtual-machine"></a>Egyéni adatok beszúrva közzététele az Azure virtuális gépen
Ez a funkció jelenleg csak a támogatott a [Azure parancssori felület](https://github.com/Azure/azure-xplat-cli). Itt létrehozhatunk egy `custom-data.txt` fájlt, amely tartalmazza az adatokat, majd szúrjon, hogy a virtuális gép kiépítése során. De használhat, a beállításokat a `azure vm create` parancsot a következő példa bemutatja egy egyszerű módszer:

```
    azure vm create <vmname> <vmimage> <username> <password> \  
    --location "West US" --ssh 22 \  
    --custom-data ./custom-data.txt  
```


## <a name="using-custom-data-in-the-virtual-machine"></a>Egyéni adatok használata a virtuális gép
* Ha az Azure virtuális gép egy Windows-alapú virtuális Gépet, akkor az egyéni adatok fájlt ment `%SYSTEMDRIVE%\AzureData\CustomData.bin`. Bár a helyi számítógépről át az új virtuális gép base64-kódolású volt, akkor automatikusan dekódolt és megnyitható vagy azonnal.
  
  > [!NOTE]
  > Ha a fájl létezik, a rendszer felülírja. A könyvtár biztonsági beállítása **System: Full Control** és **rendszergazdák: Full Control**.
  > 
  > 
* Ha az Azure virtuális gép egy Linux-alapú virtuális Gépet, majd az egyéni adatok fájl található a distro függően az alábbi helyek egyikét. Valószínűleg az adatok base64 kódolású, szükség lehet az adatok első dekódolási:
  
  * `/var/lib/waagent/ovf-env.xml`
  * `/var/lib/waagent/CustomData`
  * `/var/lib/cloud/instance/user-data.txt` 

## <a name="cloud-init-on-azure"></a>Azure cloud inicializálás
Ha az Azure virtuális gép Ubuntu vagy CoreOS lemezképből CustomData egy felhő-config küldendő felhő inicializálás használhatja. Vagy ha az egyéni fájlját egy parancsfájlt, majd felhő inicializálás végrehajtható.

### <a name="ubuntu-cloud-images"></a>Ubuntu felhő lemezképek
A legtöbb Azure Linux-lemezképekben volna szerkesztése "/ etc/waagent.conf" ideiglenes fájl és az ideiglenes erőforrás lemez konfigurálni. Lásd: [Azure Linux ügynök felhasználói útmutató](../articles/virtual-machines/extensions/agent-linux.md) további információt.

Azonban az Ubuntu felhő képek kell használnia felhő inicializálás konfigurálása az erőforrás (Ez azt jelenti, hogy a "elmúló") lemez és partíció felcserélése. A következő oldal jelenik meg a további részletekért Ubuntu wiki: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps-using-cloud-init"></a>További lépések: Felhő inicializálás használatával
További információkért lásd: a [Ubuntu felhő inicializálás dokumentációja](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Adja hozzá a szerepkör Service Management REST API-referencia](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Azure parancssori felület](https://github.com/Azure/azure-xplat-cli)

