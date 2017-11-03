

## <a name="multi-and-single-instance-vms"></a>Többszörös és egypéldányos virtuális gépek
Sok ügyfél futó Azure száma akkor fontos, hogy azok ütemezhető a virtuális gépek tervezett karbantartás miatt az állásidő--változni mintegy 15 percre leáll--, amely akkor fordul elő, karbantartás során. Rendelkezésre állási csoportok segítségével szabályozhatja, amikor kiosztott virtuális gépek tervezett karbantartás.

Nincsenek Azure-on futó virtuális gépek két lehetséges konfigurációkat. Virtuális gépek vagy többpéldányos vagy egypéldányos kell konfigurálni. Ha virtuális gépek rendelkezésre állási csoportba, majd vannak konfigurálva több példányt. Vegye figyelembe, még akkor is egyetlen, virtuális gépek is telepíthető egy rendelkezésre állási csoportot, hogy több példányt kezelje őket. Ha a virtuális gépek nincsenek egy rendelkezésre állási csoportot, majd vannak konfigurálva, Egypéldányos.  További részletek a rendelkezésre állási csoportok: [a Windows virtuális gépek rendelkezésre állásának kezelése](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vagy [a Linux virtuális gépek rendelkezésre állásának kezelése](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Egypéldányos és többpéldányos virtuális gépek tervezett karbantartás frissítések külön-külön fordulhat elő. A virtuális gépek kell egypéldányos (ha többpéldányos) vagy (ha egypéldányos) többpéldányos újrakonfigurálásával szabályozhatja, ha a virtuális Gépeik kapni a tervezett karbantartáshoz. Lásd: [a tervezett karbantartások Azure Linux virtuális gépek](../articles/virtual-machines/linux/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [a tervezett karbantartásról, a Azure Windows virtuális gépek](../articles/virtual-machines/windows/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Azure virtuális gépek tervezett karbantartása leírását.

## <a name="for-multi-instance-configuration"></a>Többpéldányos konfiguráció
Kiválaszthatja, hogy a tervezett karbantartások hatással van a virtuális gépek üzembe helyezett egy rendelkezésre állási csoport konfigurációs feloldhatja a virtuális gépek rendelkezésre állási készletek idő.

1. E-mailt kap hét nappal a többpéldányos konfigurációban a virtuális gépek tervezett karbantartás előtt. Az előfizetés-azonosítók és a többpéldányos érintett virtuális gépek nevét az e-mail törzsében szerepelnek.
2. E hét napban kiválaszthatja a frissítésekor a példányok vannak az adott régióban eltávolításával a többpéldányos virtuális gépek a rendelkezésre állási csoport. Ez a változás a konfigurációban újraindítás, azt eredményezi, mint egy fizikai gazdagép karbantartás, egy másik fizikai gazdagépre, amely nem érvényes az karbantartási szánt továbbítása a virtuális gép.
3. A virtuális gép eltávolíthatja a rendelkezésre állási csoportban, az Azure portálon.

   1. A portálon válassza ki a virtuális gép eltávolítása a rendelkezésre állási csoportban.  

   2. A **beállítások**, kattintson a **rendelkezésre állási csoport**.

      ![Rendelkezésre állási csoport kiválasztása](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)

   3. A rendelkezésre állási csoport legördülő menüre, válassza ki a "Nem részei egy rendelkezésre állási csoportnak."

      ![Távolítsa el a készletből](./media/virtual-machines-planned-maintenance-schedule/availabilitysetwarning.png)

   4. A lap tetején kattintson **mentése**. Kattintson a **Igen** megerősíti, hogy ez a művelet újraindítja a virtuális gép számára.

   >[!TIP]
   >A virtuális gépek a többpéldányos később újrakonfigurálhatja a listában szereplő rendelkezésre állási csoportok kiválasztásával.

4. Virtuális gépek rendelkezésre állási készletek távolítva egypéldányos állomások kerülnek, és nem frissülnek a rendelkezésre állási beállítása beállításokat a tervezett karbantartás alatt.
5. A rendelkezésre állási beállítása virtuális gépekhez a frissítés befejezése után (megfelelően az eredeti e-mailt leírt ütemezés), adja hozzá a virtuális gépek a rendelkezésre állási készletek programba. Rendelkezésre állási csoport tagjaivá újrakonfigurálja a virtuális gépek több példányt, és a újraindítását eredményezi. Általában ha minden többpéldányos frissítés a teljes Azure-környezetéhez között megadta, Egypéldányos karbantartási követi.

A virtuális gép eltávolítása a rendelkezésre állási csoport is elérhető Azure PowerShell használatával:

```
Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM
```

## <a name="for-single-instance-configuration"></a>Egypéldányos konfiguráció
Kiválaszthatja, hogy a tervezett karbantartások hatással van, virtuális gépek egypéldányos konfigurációban adja hozzá a virtuális gépeken a rendelkezésre állási készletek idő.

Lépésről lépésre

1. E-mailt kap hét nappal egypéldányos konfigurációban a virtuális gépek tervezett karbantartás előtt. Az előfizetés-azonosítók és a érintett egypéldányos virtuális gépek nevét az e-mail törzsében szerepelnek.
2. E hét napban kiválaszthatja az idő, a példány újraindítja az Egypéldányos virtuális gépek rendelkezésre állási készlet ugyanabban a régióban való hozzáadásával. Ez a változás a konfigurációban újraindítás, azt eredményezi, mint egy fizikai gazdagép karbantartás, egy másik fizikai gazdagépre, amely nem érvényes az karbantartási szánt továbbítása a virtuális gép.
3. Kövesse az utasításokat itt meglévő virtuális gépek rendelkezésre állási készletek használata az Azure portál és az Azure PowerShell hozzáadni. (Lásd az Azure PowerShell-példa, amely ezeket a lépéseket.)
4. Miután a virtuális gépek több példányt voltak, tartoznak a a tervezett karbantartások egypéldányos virtuális gépekhez.
5. Az Egypéldányos VM frissítése (futtatása ütemezés szerint az eredeti e-mailben) után visszatérhet a virtuális gépek egypéldányos feloldhatja a virtuális gépek a rendelkezésre állási készletek.

A virtuális gépek felvétele a rendelkezésre állási készlet is elérhető Azure PowerShell használatával:

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/
