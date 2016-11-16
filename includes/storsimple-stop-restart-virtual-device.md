#### <a name="to-stop-and-start-a-virtual-device"></a>Virtuális eszköz leállítása és indítása
Egy virtuális eszköz leállításához kattintson a nevére, majd kattintson a **Leállítás** elemre. A virtuális eszköz leállítása közben az eszköz állapota **Leállítás folyamatban**. A virtuális eszköz leállítás utáni állapota **Leállítva**.

Virtuális eszközt az alábbi parancsmagok segítségével állíthatja le és indíthatja újra.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-virtual-device"></a>Virtuális eszköz újraindítása
Ha egy virtuális eszközt futás közben szeretne újraindítani, kattintson a nevére, majd az **Újraindítás** lehetőségre. A virtuális eszköz újraindítása közben az eszköz állapota **Újraindítás folyamatban**. Ha a virtuális eszköz használatra kész, az eszköz állapota **Fut**.

Az alábbi parancsmag segítségével indítsa újra a virtuális eszközt.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`



<!--HONumber=Nov16_HO2-->


