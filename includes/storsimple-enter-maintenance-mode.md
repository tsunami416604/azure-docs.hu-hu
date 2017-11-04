<!--author=SharS last changed: 12/01/15-->

#### <a name="to-enter-maintenance-mode"></a>Adja meg a karbantartási módból való
1. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes körű hozzáférési**.
2. Írja be a jelszót. Az alapértelmezett jelszó **jelszó1**.
3. Írja be a parancssorba
   
     `Enter-HcsMaintenanceMode`
4. Látni fogja, hogy a karbantartási mód lesz megzavarhatják összes i/o-kérelmek és -kiszolgálón a kapcsolat a klasszikus Azure portálra, és megerősítés bekéri felszólító figyelmeztető üzenetet. Típus **Y** a karbantartási üzemmódba.
   
    Mindkét tartományvezérlők újraindul. Ha az újraindítás befejeződött, egy másik üzenet jelenik meg arról, hogy az eszköz karbantartási módban.

