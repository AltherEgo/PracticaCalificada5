
1.	En las actividades relacionados a la Introducción de Rails los métodos actuales del controlador no son muy robustos: si el usuario introduce de manera manual un URI para ver (Show) una película que no existe (por ejemplo /movies/99999), verás un mensaje de excepción horrible. Modifica el método show del controlador para que, si se pide una película que no existe, el usuario sea redirigido a la vista Index con un mensaje más amigable explicando que no existe ninguna película con ese.

Respuesta: para informar de algún error podemos usar flash en nuestro controlador, con este podemos recibir una alerta de algún error o exito, dicho esto lo agregamos

```ruby
  def show
    @movie = Movie.find_by(id: params[:id])

    if @movie.nil?
      flash[:notice] = 'No existe dicha pelicula'
      redirect_to movies_path
    else
      #Flujo normal del programa
    end
  end
```

2.	En las actividades relacionados a Rails Avanzado, si tenemos el siguiente ejemplo de código que muestra cómo se integra OmniAuth en una aplicación Rails:
```ruby
class SessionsController < ApplicationController
 	   def create
    	         @user = User.find_or_create_from_auth_hash(auth_hash)
    	         self.current_user = @user
    	         redirect_to '/'
                   end
          	  protected
          	  def auth_hash
            		request.env['omniauth.auth']
                 end
              end
```
El método auth_hash  tiene la sencilla tarea de devolver lo que devuelva OmniAuth como 
resultado de intentar autenticar a un usuario. ¿Por qué piensa que se colocó esta funcionalidad 
en su propio método en vez de simplemente referenciar request.env[’omniauth.auth’] 
directamente? Muestra el uso del script.

Respuesta: El uso de variables de entorno nos permite un compartir nuestro proyecto sin preocuparnos de filtrar información delicada,  por esta razón se utiliza auth_has de tipo protected para que no se pueda acceder desde otras clases a esta respuesta de la variable de entorno y evitar conflictos

3.	En las actividades relacionados a JavaScript, Siguiendo la estrategia del ejemplo de jQuery utiliza JavaScript para implementar un conjunto de casillas de verificación (checkboxes) para la página que muestra la lista de películas, una por cada calificación (G, PG, etcétera), que permitan que las películas correspondientes permanezcan en la lista cuando están marcadas. Cuando se carga la página por primera vez, deben estar marcadas todas; desmarcar alguna de ellas debe esconder las películas con la clasificación a la que haga referencia la casilla desactivada.

```html
<!-- Agrega las casillas de verificación con las clasificaciones de películas -->
<input type="checkbox" id="rating-G" checked> G
<input type="checkbox" id="rating-PG" checked> PG
<!-- Agrega más casillas de verificación según las clasificaciones -->

<!-- Lista de películas -->
<ul id="movieList">
  <li data-rating="G">Película con clasificación G</li>
  <li data-rating="PG">Película con clasificación PG</li>
  <!-- Agrega más elementos li según las clasificaciones de tus películas -->
</ul>

<script>
  // Asegúrate de que este código se ejecute después de que la página se haya cargado
  document.addEventListener("DOMContentLoaded", function () {
    // Obtén todas las casillas de verificación por su selector
    const checkboxes = document.querySelectorAll('input[type="checkbox"]');

    // Agrega un event listener a cada casilla de verificación
    checkboxes.forEach(function (checkbox) {
      checkbox.addEventListener('change', function () {
        // Obtén la clasificación de la casilla de verificación actual
        const rating = checkbox.id.replace('rating-', '');

        // Obtén todas las películas con la clasificación correspondiente
        const movies = document.querySelectorAll(`li[data-rating="${rating}"]`);

        // Muestra u oculta las películas según si la casilla está marcada o no
        movies.forEach(function (movie) {
          movie.style.display = checkbox.checked ? 'block' : 'none';
        });
      });
    });
  });
</script>
```

Respuesta: esta funcionalidad se implementó en la PC3 sin embargo sin el uso de javascript, ahora agregando javascript podemos desaparecer las peliculas no marcadas en la misma vista sin necesidad de recargar la página, gracias al display none, que es muy útil al momento de no mostrar algún elemento


4.	De la actividad relacionada a BDD e historias de usuario crea definiciones de pasos que te permitan escribir los siguientes pasos en un escenario de RottenPotatoes:

Given the movie "Inception" exists
	And it has 5 reviews
	And its average review score is 3.5


```cucumber
#Inicialmente se tiene que crear una película
Given(/^the movie "(.*?)" exists$/) do |movie_title|
  
  Movie.create(title: movie_title)

end

# Asignación de cantidad de reviews para la película a crear
Given(/^it has (\d+) reviews$/) do |review_count|
  
  movie = Movie.last
  review_count.to_i.times do
    movie.reviews.create
  end
end
#Asignación de una calificación promedio de los puntajes
Given(/^its average review score is (\d+\.\d+)$/) do |average_score|

  movie = Movie.last
  movie.reviews.each { |review| review.update(score: average_score.to_f / movie.reviews.count) }
end
```
Respuesta: En cucumber podemos usar BDD que nos permiten conocer el comportamiento de nuestra aplicación, esto nos facilita mayor y ligera comprensión del proyecto. Es así que se definen los comportamientos para el caso de crear la pelicula, la cantidad de reseñas y el promedio de estas mismas


5.	De la actividad relacionadas a BDD e historias de usuario, supongamos que en RottenPotatoes, en lugar de utilizar seleccionar la calificación y la fecha de estreno, se opta por rellenar el formulario en blanco. Primero, realiza los cambios apropiados al escenario. Enumera las definiciones de pasos a partir que Cucumber invocaría al pasar las pruebas de estos nuevos pasos. (Recuerda: rails generate cucumber:install)

**Parte 2**
1.	Completa el escenario restrict to movies with PG or R ratings in filter_movie_list.feature. Puedes utilizar las definiciones de pasos existentes en web_steps.rb para marcar y desmarcar las casillas correspondientes, enviar el formulario y comprobar si aparecen las películas correctas (y, lo que es igualmente importante, no aparecen las películas con clasificaciones no seleccionadas).

Respuesta: Se necesita modificar el feature para poder ver un correcto comportamiento en nuestra aplicación usando bdd  

 ```cucumber
 Scenario: restrict to movies with PG or R ratings
  Given I am on the RottenPotatoes home page
  When I check the "PG" checkbox
  And I check the "R" checkbox
  And I press "Refresh Page"
  Then I should see only movies with ratings: PG, R
  And I should not see movies with other ratings
```
2.	Dado que es tedioso repetir pasos como When I check the "PG" checkbox, And I check the "R" checkbox, etc., crea una definición de paso que coincida con un paso como, por ejemplo: Given I check the following ratings: G, PG, R
Esta definición de un solo paso solo debe marcar las casillas especificadas y dejar las demás 	casillas como estaban.

Respuesta: Necesitamos revisar el check de las clases, usamos split y en el step lo checkeamos

```ruby
 Given(/^I check the following ratings: (.+)$/) do |ratings|
  ratings.split(', ').each do |rating|
    step "I check the \"#{rating}\" checkbox"
  end
end
```
3.	 Dado que es tedioso especificar un paso para cada película individual que deberíamos ver, agrega una definición de paso para que coincida con un paso como: “Then I should see the following movies".
4.	Para el escenario all ratings selected sería tedioso utilizar And I should see para nombrar cada una de las películas. Eso restaría valor al objetivo de BDD de transmitir la intención de comportamiento de la historia del usuario. Para solucionar este problema, completa la definición de paso que coincida con los pasos del formulario: Then I should see all the movies  en movie_steps.rb. 
Considera contar el número de filas en la tabla HTML para implementar estos pasos. Si ha 	calculado las filas como el número de filas de la tabla, puede usar la afirmación expect(rows).to 	eq value para fallar la prueba en caso de que los valores no coincidan.

5.	Utiliza tus nuevas definiciones de pasos para completar el escenario con todas las calificaciones seleccionadas. Todo funciona bien si todos los escenarios en filter_movie_list.feature pasan con todos los pasos en verde.

**Parte 3**

1.	Describa uno o más patrones de diseño que podrían ser aplicados al diseño del sistema.
 Usamos de ejemplo ![Alt text](image.png)

Este es un sistema que ayuda a calificar automaticamente los examenes y permite reducir los plageos y las tardanzas, etc.

Patrón Strategy:

    En este caso, podrías tener diferentes estrategias de calificación para diferentes tipos de exámenes o evaluaciones. Y definir una diferente estrategia para cada una

Patrón Template Method:

    Puedes tener una clase base que define un esquema general de calificación y luego derivar clases específicas para implementar la lógica detallada de calificación para cada tipo de examen.

Patrón Observer:

    Los observadores(alumnos o profesores) pueden ser notificados cuando se actualizan las calificaciones, y esto puede ser útil para enviar notificaciones o informes automáticos.


2.	Dado un sistema simple que responde a una historia de usuario concreta, analice y elija un paradigma de diseño adecuado

Necesitamos que los usuarios estén enterados y responda debido a los diferentes sucesos, pueden ocurrir eventos inesperados por eso puede ser:
Programación Basada en Eventos:

    Consideraciones:
        Si la historia de usuario implica la gestión de eventos y notificaciones.
        Si se busca desacoplar componentes y permitir una mayor extensibilidad.

    Ejemplo:
        Si la historia de usuario implica la creación de un sistema de notificaciones donde diferentes partes del sistema pueden reaccionar a eventos específicos.


3.	Analice y elija una arquitectura software apropiada que se ajuste a una historia de usuario concreta de este sistema. ¿La implementación en el sistema de esa historia de usuario refleja su idea de arquitectura?

# PracticaCalificada5
