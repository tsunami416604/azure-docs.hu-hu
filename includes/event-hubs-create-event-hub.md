## Eseményközpont létrehozása

1. Jelentkezzen be a [klasszikus Azure portálra][], és kattintson az **ÚJ** gombra a képernyő alján.

2. Kattintson az **Alkalmazásszolgáltatások**, majd a **Service Bus**, az **Eseményközpont**, végül pedig a **Gyorslétrehozás** elemre.

    ![][1]

3. Adjon meg egy nevet az Eseményközpont számára, válassza ki a kívánt régiót, majd kattintson az **Új eseményközpont létrehozása** gombra.

    ![][2]

4. Ha nem választott ki egy már létező névteret egy adott régióban, a portál létrehoz egyet (általában a következők szerint: ***eseményközpont neve*-ns**). Kattintson a névtérre (ebben a példában: **eventhub-ns**).

    ![][3]

5. Kattintson a lap alján található **Kapcsolatadatok** gombra. Kattintson (az alábbi ábrán kiemelt) másolás gombra, hogy a **RootManageSharedAccessKey** kapcsolati karakterláncot a vágólapra másolja. Mentse el ezt a kapcsolati karakterláncot a jelen oktatóprogramban való későbbi használat céljából.

    ![][4]

Létrejött az Eseményközpont, és most már rendelkezik az események küldéséhez és fogadásához szükséges kapcsolati karakterlánccal.

[1]: ./media/event-hubs-create-event-hub/create-event-hub1.png
[2]: ./media/event-hubs-create-event-hub/create-event-hub2.png
[3]: ./media/event-hubs-create-event-hub/create-event-hub3.png
[4]: ./media/event-hubs-create-event-hub/create-conn-str1.png


<!--HONumber=Jun16_HO2-->


