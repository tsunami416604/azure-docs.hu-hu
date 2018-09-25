


Ez a cikk azt ismerteti, hogyan lehet:

* Adatok betöltése egy Azure virtuális gép (VM) be, amikor kiépítése folyamatban van.
* Beolvasni a Windows és Linux egyaránt.
* Egyes rendszerek elérhető speciális eszközök használatával észlelheti és egyéni adatok automatikusan kezeli.

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan egyéni adatok juttatható a klasszikus üzemi modellel létrehozott virtuális gép használatával. Az Azure Resource Management API használatával, olvassa el [a példasablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).
> 
> 

## <a name="injecting-custom-data-into-your-azure-virtual-machine"></a>Egyéni adatok betöltése az Azure virtuális géphez

[!INCLUDE [classic-cli](contains-classic-cli-content.md)]

Ez a funkció jelenleg csak a támogatott a [Azure CLI-vel](https://github.com/Azure/azure-xplat-cli). Itt létrehozunk egy `custom-data.txt` fájlt, amely tartalmazza az adatokat, majd beszúrása, hogy a virtuális gép kiépítése során. Bár használhatja a beállításokat a a `azure vm create` parancsot, a következő példa bemutatja egy egyszerű módszert:

```
    azure vm create <vmname> <vmimage> <username> <password> \  
    --location "West US" --ssh 22 \  
    --custom-data ./custom-data.txt  
```


## <a name="using-custom-data-in-the-virtual-machine"></a>Egyéni adatok használata a virtuális gép
* Ha az Azure virtuális gép egy Windows-alapú virtuális Gépet, akkor az egyéni adatfájl mentett `%SYSTEMDRIVE%\AzureData\CustomData.bin`. Bár a helyi számítógépről át az új virtuális gép base64-kódolású volt, azt automatikusan dekódolt, és megnyitható vagy azonnal.
  
  > [!NOTE]
  > Ha a fájl létezik, a rendszer felülírja. A könyvtár biztonsági értékre van állítva **System: Full Control** és **rendszergazdák: Full Control**.
  > 
  > 
* Ha az Azure virtuális gép egy Linux-alapú virtuális Gépet, majd az egyéni adatfájl található a disztribúció függően az alábbi helyek egyikét. Az adatok lehetnek, base64-kódolású, ezért szükség lehet az adatok első dekódolási:
  
  * `/var/lib/waagent/ovf-env.xml`
  * `/var/lib/waagent/CustomData`
  * `/var/lib/cloud/instance/user-data.txt` 

## <a name="cloud-init-on-azure"></a>A cloud-init az Azure-ban
Ha az Azure virtuális gép egy Ubuntu vagy a CoreOS rendszerképből, majd használhatja a CustomData a felhőkonfiguráció küldeni a cloud-init. Vagy ha az egyéni adatfájlt egy parancsfájlt, majd a cloud-init is futtathatja.

### <a name="ubuntu-cloud-images"></a>Ubuntu felhő-rendszerképek
A legtöbb Azure Linux-rendszerképeket, szeretné szerkeszteni "/ etc/waagent.conf" az ideiglenes erőforrás-lemez beállításához és az ideiglenes fájl. Lásd: [Azure Linux-ügynök használati útmutatója](../articles/virtual-machines/extensions/agent-linux.md) további információt.

Azonban az Ubuntu, felhő-rendszerképek a kell használnia a cloud-init konfigurálása a erőforrás (azaz a "rövid élettartamú") lemez és partíció cseréje. További részletekért Ubuntu wiki a következő oldal jelenik meg: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps-using-cloud-init"></a>Következő lépések: a cloud-init használata
További információkért lásd: a [Ubuntu dokumentációjában, a cloud-init](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Adja hozzá a szerepkör Service Management REST API-referencia](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Az Azure parancssori felület](https://github.com/Azure/azure-xplat-cli)

