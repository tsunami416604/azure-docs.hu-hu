* A Távoli asztali kapcsolat funkcióval létesítsen kapcsolatot a folyamatkiszolgáló virtuális géppel.
* A cspsconfigtool.exe fájlt az asztalon található parancsikonra kattintva futtathatja. (Ha első alkalommal jelentkezik be a folyamatkiszolgálóra, az eszköz automatikusan elindul).
  - A konfigurációs kiszolgáló teljes neve (FQDN) vagy IP-címe.
  - Az a port, amelyen a konfigurációs kiszolgáló figyel. Az értéknek 443-nak kell lennie.
  - A kapcsolat hozzáférési kódja, hogy kapcsolódni lehessen a konfigurációs kiszolgálóhoz.
  - A folyamatkiszolgálóhoz konfigurált adatátviteli port. Hagyja változatlanul az alapértelmezett értéket, kivéve, ha egy másik portszámra módosította a saját környezetében.

    ![A folyamatkiszolgáló regisztrálása](./media/site-recovery-vmware-register-process-server/register-ps.png)
* A konfiguráció mentéséhez és a Folyamatkiszolgáló regisztrálásához kattintson a Mentés gombra.
