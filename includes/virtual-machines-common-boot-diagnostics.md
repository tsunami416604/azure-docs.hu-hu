Mostantól két hibakereső szolgáltatás is elérhető az Azure-ban: konzolkimenet és képernyőkép is támogatott az Azure virtuális gépeken a Resource Manager-alapú üzemi modellben. 

Amikor a saját rendszerképét használja az Azure-ban, vagy valamelyik platform rendszerképét indítja, számos oka lehet annak, hogy egy virtuális gép rendszerindításra képtelen állapotba kerül. Ezekkel a szolgáltatásokkal könnyedén diagnosztizálhatja és helyreállíthatja a virtuális gépeket a rendszerindítási hibák után.

Linux virtuális gépek esetén a konzol naplófájljának kimenetét egyszerűen megtekintheti a Portalon:

![Azure Portal](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)
 
Azonban az Azure Windows és Linux virtuális gépeken is lehetővé teszi, hogy megtekintsen egy képernyőképet a virtuális gépről a hipervizortól:

![Hiba](./media/virtual-machines-common-boot-diagnostics/screenshot2.png)

Mindkét szolgáltatás támogatott az Azure virtuális gépeken minden régióban. Ne feledje, akár 10 percet is igénybe vehet, hogy a képernyőképek és a kimenet megjelenjen a tárfiókjában.

## <a name="common-boot-errors"></a>Gyakori rendszerindítási hibák

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Nem található operációs rendszer](https://support.microsoft.com/help/4010142)
- [Rendszerindítási hiba vagy INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Diagnosztika engedélyezése egy új virtuális gépen
1. Amikor új virtuális gépet hoz létre a Betekintő portálon, válassza az **Azure Resource Manager** lehetőséget az üzemi modell legördülő menüből:
 
    ![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. Konfigurálja a Figyelés beállítást, és válassza ki a tárfiókot, ahova el kívánja helyezni ezeket a diagnosztikai fájlokat.
 
    ![Virtuális gép létrehozása](./media/virtual-machines-common-boot-diagnostics/screenshot4.jpg)

3. Ha egy Azure Resource Manager-sablonból végzi a központi telepítést, keresse meg a virtuális gép erőforrást, és fűzze hozzá a diagnosztikai profil szakaszt. Fontos, hogy a „2015-06-15” API-verzió fejlécet használja.

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. A diagnosztikai profil lehetővé teszi, hogy kiválassza a tárfiókot, ahol el kívánja helyezni ezeket a naplókat.

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

Engedélyezett rendszerindítási diagnosztikával futó minta virtuális gép telepítéséhez tekintse meg a tárházunkat.

## <a name="update-an-existing-virtual-machine"></a>Meglévő virtuális gép frissítése ##

Ha a portálon keresztül kívánja engedélyezni a rendszerindítási diagnosztikát, frissíthet egy meglévő virtuális gépet is. Válassza a Rendszerindítási diagnosztika lehetőséget, majd kattintson a Mentés elemre. A módosítás a virtuális gép újraindítása után lép életbe.

![Létező virtuális gép frissítése](./media/virtual-machines-common-boot-diagnostics/screenshot5.png)

