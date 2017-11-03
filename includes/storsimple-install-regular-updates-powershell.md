<!--author=SharS last changed: 11/18/16-->

#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>A StorSimple rendszeres frissítéseket a Windows PowerShell telepítése
1. Nyissa meg az eszköz soros konzoljához és select 1. lehetőség – **jelentkezzen be a teljes körű hozzáférési**. Írja be a jelszót. Az alapértelmezett jelszó *jelszó1*. 
2. A parancssorba írja be:
   
     `Get-HcsUpdateAvailability`
   
    Ha frissítések érhetők el, és hogy a frissítések zavaró vagy nem zavaró, értesítést fog kapni.
3. A parancssorba írja be:
   
     `Start-HcsUpdate`
   
    A frissítési folyamat indul el.

> [!IMPORTANT]
> * Ez a parancs csak a rendszeres frissítések vonatkozik. A parancs futtatása csak egy tartományvezérlőn, de mindkét tartományvezérlők frissülni fog. 
> * A vezérlő feladatátvétel Észreveheti a frissítés során; azonban a feladatátvétel nem érinti a rendszer rendelkezésre állás vagy a műveletet.
> 
> 

