<!--author=alkohli last changed: 08/29/17-->

## <a name="troubleshooting-update-failures"></a>A frissítéssel kapcsolatos hibák elhárítása
**Mi a teendő, ha megjelenik egy értesítés, hogy a frissítés előtti ellenőrzések sikertelenek voltak?**

Ha egy előzetes ellenőrzés sikertelen, tekintse meg az oldal alján lévő részletes értesítési sávot. Ez útmutatást nyújt arról, hogy miért volt sikertelen az előzetes ellenőrzés. Például kap értesítést, amelyek a tartományvezérlő állapotának ellenőrzése és a hardver összetevő állapot-ellenőrzése sikertelen volt. Ugrás a **figyelő > hardver állapotának**. Győződjön meg arról, hogy mindkét tartományvezérlők kifogástalan és online kell. Szükség győződjön meg arról, hogy a StorSimple eszköz minden hardverösszetevő megjelennek-e a lesz kifogástalan, ezen a panelen. Ezután próbálja meg telepíteni a frissítéseket. Ha nem tudja kijavítani a hardverösszetevő problémáit, a további lépésekért forduljon a Microsoft ügyfélszolgálatához.

**Mi a teendő, ha „A frissítéseket nem lehet telepíteni” hibaüzenetet kap, és a rendszer azt javasolja, hogy tekintse meg a frissítési hibaelhárítási útmutatót a hiba okának meghatározásához?**

Ennek egy valószínű oka lehet, hogy nem csatlakozik a Microsoft Update-kiszolgálókhoz. Ezt manuálisan kell ellenőrizni. Ha megszakad a kapcsolat a frissítési kiszolgálóval, a frissítési feladat meghiúsul. A kapcsolat ellenőrzéséhez futtassa a következő parancsmagot a StorSimple-eszköz Windows PowerShell-felületén:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Futtassa a parancsmagot mindkét vezérlőn.

Ha ellenőrizte, hogy a kapcsolat létezik, és továbbra is ezt a hibát látja, a további lépésekért forduljon a Microsoft ügyfélszolgálatához.

**Mi a teendő, ha frissítési hiba történik, amikor az eszközt a 4-es frissítésre frissíti, és mindkét vezérlő a 4-es frissítést futtatja?**

A 4-es frissítéstől kezdődően, ha mindkét vezérlő ugyanazt a szoftververziót futtatja és frissítési hiba fordul elő, a vezérlők nem lépnek helyreállítási módba. Ez a helyzet akkor fordulhat elő, ha az eszközszoftver gyorsjavítását (elsőrendű frissítés) mindkét vezérlőre sikeresen alkalmazza, de még további gyorsjavításokat (másod- és harmadrendű frissítéseket) kell alkalmazni. A 4-es frissítéstől kezdve a vezérlők csak akkor lépnek helyreállítási módba, ha a két vezérlőn eltérő szoftververzió fut. 

Ha frissítési hiba jelenik meg, amikor mindkét vezérlő a 4-es frissítést futtatja, javasoljuk, hogy várjon néhány percet, majd próbálkozzon újra a frissítéssel. Ha az ismételt próbálkozás nem sikerül, forduljon a Microsoft ügyfélszolgálatához.
