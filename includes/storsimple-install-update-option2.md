<!--author=SharS last changed: 03/17/2016-->

#### <a name="to-install-update-12-from-the-azure-classic-portal"></a>1.2-es frissítés telepítése a klasszikus Azure portálon
1. A klasszikus Azure portálon, válassza a **eszközök** lapon, és válassza ki azt az eszközt.
2. Navigáljon a **eszközök** > **konfigurálása**.
3. A **hálózati illesztők**, először ellenőrizze, hogy rendelkezik-e legalább egy hálózati adapter, amely az iSCSI-kompatibilis. Keresse meg a hálózati adapter (mint a DATA 0), amely rendelkezik hozzárendelt átjáró.
4. Tiltsa le a hálózati adapter, amelyen egy hozzárendelt átjáró, és mentse a módosított konfigurációs. Vegye figyelembe a hálózati kapcsolati beállítások megmaradnak, és ezért ha újra engedélyezi a hálózati illesztő később, a portál visszaáll az eredeti beállításokat.
5. Most [1.2-es frissítés telepítése a klasszikus Azure portál használatával](#install-update-12-via-the-azure-classic-portal). Kövesse az utasításokat, ez az eljárás 3. lépés-től kezdődő. A frissítések telepítése után újra engedélyezheti a letiltott hálózati kapcsolat.

