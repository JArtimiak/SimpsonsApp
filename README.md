Primer Error:
Ubicación: app/src/main/java/com/example/simpsonsapp/domain/model/Episode.kt
Problema: Hay un bloque init { return Episode; } fuera de la definición de la data class.
Los bloques init deben estar dentro de una clase. Además, un bloque init no puede retornar valores.
Solución: Eliminar ese bloque de código al final del archivo.

Segundo Error:
Error de Configuración de Retrofit
Ubicación: app/src/main/java/com/example/simpsonsapp/di/DataModule.kt (Función provideSimpsonsApi)
Problema: El Retrofit.Builder() no tiene configurado el .baseUrl(). Retrofit lanzará un error al intentar construir la instancia .build().
Solución: Añadir .baseUrl("https://thesimpsonsapi.com/") (o la URL que corresponda) antes de llamar a .build().

Tercer Error:
Error de Compilación por Falta de Importación
Ubicación: app/src/main/java/com/example/simpsonsapp/data/repository/EpisodeRepositoryImpl.kt
Problema: La clase está utilizando SimpsonsApi, pero no se ha importado. 
Como SimpsonsApi está en un paquete diferente (data.remote), esto provoca un error de compilación.
Solución: Agregar import com.example.simpsonsapp.data.remote.SimpsonsApi.

Cuarto Error:
Ubicación: app/src/main/java/com/example/simpsonsapp/domain/repository/EpisodeRepository.kt y EpisodeRepositoryImpl.kt
Problema: En la interfaz, el método se llama get_episodes(), pero en la implementación se llama getEpisodes(). 
Esto causará un error de compilación porque la clase no está cumpliendo con el contrato de la interfaz.
Solución: Unificar ambos nombres a getEpisodes().

Quinto Error:
Ubicación: app/src/main/java/com/example/simpsonsapp/main/MainScreen.kt (líneas 51-53)
Problema: Hay un if que llama a viewModel.refreshSeasons() directamente en el cuerpo del Composable. 
Esto es un efecto secundario no controlado que se ejecutará en cada recomposición, 
pudiendo causar bucles infinitos y pérdida de rendimiento.
Solución: Envolver esa lógica en un LaunchedEffect para que se ejecute de forma controlada 
según el estado:

Sexto Error:
Ubicación: app/src/main/java/com/example/simpsonsapp/data/remote/EpisodeRemoteMediator.kt.
Problema: La interfaz SimpsonsApi se encuentra al final del archivo (líneas 105-110).
Esto va en contra del principio de responsabilidad única y complica la mantenibilidad.
Solución: Trasladar la interfaz SimpsonsApi a su propio archivo dentro del paquete app/src/main/java/com/example/simpsonsapp/data/remote.

Séptimo Error:
Ubicación: app/src/main/java/com/example/simpsonsapp/main/MainScreen.kt (Selector de episodios).
Problema: El botón de "Selector de Episodios" utiliza episodes.itemSnapshotList para encontrar el índice.
Esta lista solo incluye los elementos que están actualmente en memoria. Si el usuario intenta acceder al episodio 20 y se cargaron solo los primeros 10, no va a poder encontrarlo.
Solución: El salto a posiciones que no están cargadas se debe gestionar mediante una lógica de búsqueda que no dependa únicamente del snapshot actual.

Octavo Error:
Ubicación: app/src/main/java/com/example/simpsonsapp/main/MainViewModel.kt
Problema: loadSeasons() solo se ejecuta en el init.
Si la base de datos está vacía al principio, la lista de temporadas se quedará vacía para siempre a menos que se haga un refresco manual. No está observando los cambios en la base de datos.
Solución: Modificar getAvailableSeasons() en el DAO para que retorne un Flow<List<Int>> y recolectarlo en el ViewModel, garantizando que la UI se actualice automáticamente cuando Room reciba nuevos datos.

Noveno Error:
Ubicación: app/src/main/java/com/example/simpsonsapp/data/repository/EpisodeRepositoryImpl.kt (Método getEpisodesBySeason)
Problema: A diferencia de getEpisodes(), este método no hace uso de un RemoteMediator. Esto implica que si el usuario filtra por temporada, solo podrá ver lo que ya está almacenado en la base de datos local, perdiendo la opción de descargar nuevos datos de la red para ese filtro.
Solución: Implementar una lógica de Paging que también soporte el mediador para filtros específicos o garantizar una carga previa completa.

Décimo Error:
Ubicación: en dapp/src/main/java/com/example/simpsonsapp/AppNavigation.kt están las importaciones y en app/src/main/java/com/example/simpsonsapp/data/remote/EpisodeRemoteMediator.kt se encuentra la interfaz SimpsonsApi.
Problema: En la API se está utilizando la URL completa en @GET("https://thesimpsonsapi.com/api/episodes") y en la AppNavigation en (import androidx.compose.*) se usa un asterisco (*) para importar todas las clases, funciones o componentes de un paquete o módulo al mismo tiempo, en lugar de importarlos uno por uno. Esto primero limita la flexibilidad del baseUrl de Retrofit y lo segundo es una mala práctica que ensucia el espacio de nombres.
Solución: Emplear rutas relativas en @GET y hacer importaciones específicas de los componentes necesarios de Compose y Navigation.


