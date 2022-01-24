# ShoppingListTestingYT

Test a Room

/*importante para ejecutar tareas o los test uno de tras de otro*/
@ExperimentalCoroutinesApi

/*la version de junit que vamos a utilizar para los test
@RunWith(AndroidJUnit4::class)

                /* SmallTest*/    /* MediumTest*/   /* LargeTest*/
database              no         localhost only         yes
networ access         no              yes               yes
file access           no              yes               yes
external access       no          discourage            yes
multiple thread       no              yes               yes
sleep statement       no              yes               yes
system property       no              yes               yes

//alcamce de los test
@SmallTest

-------------------------------------------

@ExperimentalCoroutinesApi
@RunWith(AndroidJUnit4::class)
@SmallTest

class ShoppingDaoTest {

    @get:Rule
    var instantTaskExecutorRule = InstantTaskExecutorRule()

    private lateinit var database: ShoppingItemDatabase
    private lateinit var dao: ShoppingDao

    @Before
    fun setup() {
        database = Room.inMemoryDatabaseBuilder(
            ApplicationProvider.getApplicationContext(),
            ShoppingItemDatabase::class.java
        ).allowMainThreadQueries().build()
        dao = database.shoppingDao()
    }

    @After
    fun teardown() {
        database.close()
    }

    @Test
    fun insertShoppingItem() = runBlockingTest {
        val shoppingItem = ShoppingItem("name", 1, 1f, "url", id = 1)
        dao.insertShoppingItem(shoppingItem)

        val allShoppingItems = dao.observeAllShoppingItems().getOrAwaitValue()

        assertThat(allShoppingItems).contains(shoppingItem)
    }

    @Test
    fun deleteShoppingItem() = runBlockingTest {
        val shoppingItem = ShoppingItem("name", 1, 1f, "url", id = 1)
        dao.insertShoppingItem(shoppingItem)
        dao.deleteShoppingItem(shoppingItem)

        val allShoppingItems = dao.observeAllShoppingItems().getOrAwaitValue()

        assertThat(allShoppingItems).doesNotContain(shoppingItem)
    }

    @Test
    fun observeTotalPriceSum() = runBlockingTest {
        val shoppingItem1 = ShoppingItem("name", 2, 10f, "url", id = 1)
        val shoppingItem2 = ShoppingItem("name", 4, 5.5f, "url", id = 2)
        val shoppingItem3 = ShoppingItem("name", 0, 100f, "url", id = 3)
        dao.insertShoppingItem(shoppingItem1)
        dao.insertShoppingItem(shoppingItem2)
        dao.insertShoppingItem(shoppingItem3)

        val totalPriceSum = dao.observeTotalPrice().getOrAwaitValue()

        assertThat(totalPriceSum).isEqualTo(2 * 10f + 4 * 5.5f)
    }
}

-----------------------------
EN CASO DE UTILIZAR LIVEDATA CREA UN ARCHIVO LiveDataUtilAndroidTest.kt para simular el tiempo de respuesta utilizando el metodo getOrAwaitValue()

