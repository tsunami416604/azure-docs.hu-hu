Támogatás azokhoz a két hibakeresési funkció már elérhető az Azure-ban: konzol kimeneti és a képernyőkép támogatása az Azure virtual machines Resource Manager üzembe helyezési modellben. 

Amikor a saját kép Azure vagy akár egy platform lemezképet rendszerindítást, miért egy virtuális gép nem indítható állapotba lekérdezi számos oka lehet. Ezek a funkciók lehetővé teszik könnyen diagnosztizálhatja és a virtuális gépek helyreállítás rendszerindítási hibák esetén.

A Linux virtuális gépekhez könnyen tekintheti meg a konzol napló a portálról kimenete:

![Azure Portal](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)
 
Azonban Windows és Linux virtuális gépekhez, Azure is lehetővé teszi a virtuális Gépet a hipervizor képernyőfelvételének lásd:

![Hiba](./media/virtual-machines-common-boot-diagnostics/screenshot2.png)

Két lehetőség minden régióban Azure virtuális gépeken támogatottak. Ne feledje, akár 10 percet is igénybe vehet, hogy a képernyőképek és a kimenet megjelenjen a tárfiókjában.

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
1. Ha egy új virtuális gép létrehozása az Azure portálon, válassza ki a **Azure Resource Manager** a központi telepítési modell legördülő listából:
 
    ![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. A **beállítások**, engedélyezze a **rendszerindítási diagnosztika**, majd válassza ki, hogy szeretné-e ezek a diagnosztikai fájlok tárfiók.
 
    ![Virtuális gép létrehozása](./media/virtual-machines-common-boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > A rendszerindítási diagnosztikai funkciót nem támogatja prémium szintű storage-fiók. Rendszerindítási diagnosztika a prémium szintű tárfiókot használja, ha a StorageAccountTypeNotSupported hiba akkor fordulhat elő, amikor elindítja a virtuális Gépet.
    >
    > 

3. Ha telepíti az Azure Resource Manager sablon alapján, keresse meg a virtuálisgép-erőforrás, és hozzáfűzése a diagnosztikai profil szakasz. Fontos, hogy a „2015-06-15” API-verzió fejlécet használja.

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

A minta virtuális gép telepítése a rendszerindítási diagnosztika engedélyezve van, itt a tárházban kivenni.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>A meglévő virtuális gép rendszerindítási diagnosztika engedélyezése 

Ahhoz, hogy a rendszerindítási diagnosztika egy meglévő virtuális gépen, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com), majd válassza ki a virtuális gép.
2. A **támogatási + hibaelhárítási**, jelölje be **rendszerindítási diagnosztika** > **beállítások**, az állapot módosítása **a**, majd Válasszon egy tárfiókot. 
4. Győződjön meg arról, hogy a rendszerindítási diagnosztika beállítás van kiválasztva, és mentse a módosítást.

    ![Létező virtuális gép frissítése](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

3. A módosítás a virtuális gép újraindítása után lép életbe.


