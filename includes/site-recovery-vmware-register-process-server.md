1. A Távoli asztali kapcsolat funkcióval létesítsen kapcsolatot a folyamatkiszolgáló virtuális géppel.
2. A bejelentkezést követően azt láthatja, hogy a folyamatkiszolgáló Konfiguráció eszköze automatikusan elindul, és a következők megadását kéri:
  * A konfigurációs kiszolgáló teljes neve (FQDN) vagy IP-címe.
  * Az a port, amelyen a konfigurációs kiszolgáló figyel. Az értéknek 443-nak kell lennie.
  * A kapcsolat hozzáférési kódja, hogy kapcsolódni lehessen a konfigurációs kiszolgálóhoz.
  * A folyamatkiszolgálóhoz konfigurált adatátviteli port. Hagyja változatlanul az alapértelmezett értéket, kivéve, ha egy másik portszámra módosította a saját környezetében.

    ![A folyamatkiszolgáló regisztrálása](./media/site-recovery-vmware-register-process-server/register-ps.png)
3. A konfiguráció mentéséhez kattintson a Save (Mentés) gombra.
4. A regisztrációt követően a folyamatkiszolgáló megjelenik a konfigurációs kiszolgáló területén, és rendelkezésre áll a feladat-visszavételekhez.

> [!TIP]
> A folyamatkiszolgáló konfigurációs segédprogramjának az elindításához kattintson duplán a virtuális gép asztalán található **cspsconfigtool** parancsikonra.


<!--HONumber=Feb17_HO1-->


