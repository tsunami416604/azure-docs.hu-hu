## <a name="create-a-storage-account-by-using-the-azure-portal"></a>A storage-fiók létrehozása az Azure portál használatával

Először hozzon létre egy általános célú tárfiókot ehhez a gyors útmutatóhoz. 

1. Lépjen a [Azure-portálon](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) , és jelentkezzen be az Azure-fiók használatával. 
2. Adja meg a tárfiók egyedi nevét. Ne feledje ezeket a szabályokat a tárfiók elnevezésekor:
    - A név 3 – 24 karakter hosszúságúnak kell lennie.
    - A név számok és csak kisbetűket tartalmazhat.
3. Ellenőrizze, hogy a következő alapértelmezett értékek be vannak-e állítva: 
    - **Telepítési modell** értéke **erőforrás-kezelő**.
    - A **Fióktípus** értéke **Általános célú**.
    - A **Teljesítmény** értéke **Standard**.
    - A **Replikáció** értéke **Helyileg redundáns tárolás (LRS)**.
4. Válassza ki előfizetését. 
5. Hozzon létre egy új **Erőforráscsoportot**, és adjon meg hozzá egy egyedi nevet. 
6. Válassza ki a helyet, a tárfiók használatára.
7. Válassza ki **rögzítés az irányítópulton** válassza **létrehozása** a tárfiók létrehozásához. 

A tárfiók létrehozása után van rögzítve az irányítópulton. Válassza ki azt a megnyitásához. A **beállítások**, jelölje be **hívóbetűk**. Válassza ki az elsődleges kulcs, és másolja a társított kapcsolati karakterláncot a vágólapra. A karakterlánc majd beillesztheti egy szövegszerkesztőbe későbbi használatra.
