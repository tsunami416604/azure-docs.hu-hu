Ebben a lépésben kell megnyitni a mintavételi portot (korábban megadott 59999) elosztott terhelésű végpont tűzfalszabály létrehozása, és egy másik szabály megnyitásához a rendelkezésre állási csoport figyelőjének portszámára. Az elosztott terhelésű végpont a virtuális gépeken, amelyek tartalmazzák a rendelkezésre állási csoport replikái hozott létre, mert szükség nyissa meg a mintavételi portot és a figyelő portja a megfelelő virtuális gépeken.

1. Indítsa el a virtuális gépeken replika, **fokozott biztonságú Windows tűzfal**.

2. Kattintson a jobb gombbal **bejövő szabályok**, és kattintson a **új szabály**.

3. Az a **szabálytípus** lapon jelölje be **Port**, és kattintson a **következő**.

4. Az a **protokoll és portok** lapon jelölje be **TCP**, típus **59999** a a **adott helyi portok** gombra, majd  **Következő**.

5. Az a **művelet** lapon, tartsa **a kapcsolat engedélyezéséhez** kiválasztva, és kattintson **következő**.

6. Az a **profil** lapon fogadja el az alapértelmezett beállításokat, és kattintson a **következő**.

7. A a **neve** lap a **neve** szöveget adja meg a szabály nevét, például a **mindig a figyelő mintavételi portot**, és kattintson a **Befejezés**.

8. Ismételje meg az előző lépést a rendelkezésre állási csoport figyelőjének portszámára (a korábban a a parancsfájl $EndpointPort paraméterben megadott), majd adjon meg egy megfelelő szabályt, például a **mindig a figyelő Port**.

