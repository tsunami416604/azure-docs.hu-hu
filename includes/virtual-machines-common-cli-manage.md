Az Azure CLI 2.0 macOS, a Linux és a Windows az Azure erőforrások kezeléséhez teszi lehetővé. Ez a cikk részletesen a leggyakrabban használt parancsainak létrehozását és kezelését virtuális gépek (VM).

Ez a cikk igényel az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Is [felhő rendszerhéj](/azure/cloud-shell/quickstart) a böngészőből.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Alapszintű Azure Resource Manager-parancsok az Azure CLI-ben
Segítségért adott parancssori kapcsolók és a lehetőségek részletes, használhatja a online utasítás segítséget és beállítások beírásával `az <command> <subcommand> --help`.

### <a name="create-vms"></a>Virtuális gépek létrehozása
| Tevékenység | Az Azure parancssori felület parancsai |
| --- | --- |
| Hozzon létre egy erőforráscsoportot | `az group create --name myResourceGroup --location eastus` |
| Linux rendszerű virtuális gép létrehozása | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Windows rendszerű virtuális gép létrehozása | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>VM állapotának kezelése
| Tevékenység | Az Azure parancssori felület parancsai |
| --- | --- |
| Virtuális gép elindítása | `az vm start --resource-group myResourceGroup --name myVM` |
| Virtuális gép leállítása | `az vm stop --resource-group myResourceGroup --name myVM` |
| Virtuális gép felszabadítása | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| Virtuális gép újraindítása | `az vm restart --resource-group myResourceGroup --name myVM` |
| Virtuális gép ismételt üzembe helyezése | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| Virtuális gép törlése | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>Virtuálisgép-adatok beolvasása
| Tevékenység | Az Azure parancssori felület parancsai |
| --- | --- |
| Virtuális gépek felsorolása | `az vm list` |
| Virtuális gép adatainak lekérése | `az vm show --resource-group myResourceGroup --name myVM` |
| A virtuálisgép-erőforrások használatának megtekintése | `az vm list-usage --location eastus` |
| Minden elérhető virtuálisgép-méret megtekintése | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>A lemezek és lemezképek
| Tevékenység | Az Azure parancssori felület parancsai |
| --- | --- |
| Adatlemez hozzáadása egy virtuális géphez | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new ` |
| Adatlemez eltávolítása egy virtuális gépből | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| Lemez átméretezése | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| Lemez pillanatképének elkészítése | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| Hozzon létre egy virtuális gép képe | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| Hozzon létre virtuális Gépet lemezképből | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>Következő lépések
A parancssori felület parancsait további példákért lásd a [létrehozása és kezelése a Linux virtuális gépek az Azure parancssori felülettel](../articles/virtual-machines/linux/tutorial-manage-vm.md) oktatóanyag.

