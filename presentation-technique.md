---
header: '![w:90 h:70](images\Logo-Isika-baseline.png)'
marp: true
footer: 'Isika - AL 4 - Sofia Vayas Pedersen'
paginate: true
---

# Présentation technique 22/07/2020

## Tripping
<!-- ###  -->

---

# Plan 

 1 - Mon architecture 
 2 - Mon organisation 
 3 - Spring Boot 
 4 - Angular 
 5 - Node Js
 6 - Heroku / Git 
 7 - Demo 
 8 - Bilan 


<!-- ![bg right w:400 h:150](images\Tripping.png)  -->

---

# Mon architecture

![w:1060 h:400](images\architecture_Trip.png) 
[mon appli (localhost)](http://localhost:4200/tripping-register)

---

# Mon organisation 

![bg right w:470 h:450](images\arboraisance.png) 

- Difficultés à me lancer  

- Méthode de travail 25 min - partie solitaire (surtout pour le "last minute rush") me correspond super bien 

---

# Spring Boot

![bg right w:320 h:300](images\spring-boot-logo.png) 

[Spring Initializr](https://start.spring.io/)

Les microservices sont caractérisés par le fait qu’il est faiblement couplé. (Indépendant, facile à maintenir)

Spring boot embarque serveur tomcat (standard de JEE- port par défaut : 8080) 



---

# Modèle Microservices

![w:1060 h:450](images\model.png) 

---
# Open Feign 

Mon MS Liste ne connait pas mon MS User, donc je dois utiliser :
- Un DTO user.
- Le framework **feign netflix** permet de faire une communication dans un architecture distribué à travers des proxies

        @FeignClient(name="microservice.user")
        public interface MicroserviceUserProxy {
	        @GetMapping(path="/tripping/user/findAllUsers")
	        public List<UserBean> getAllUsers() ;
        }

Comment je récupère le proxy dans mon liste controleur? 

---

# Controleur 

- ```@Autowired```

- ``` @CrossOrigin("*") ```
- ```@Controller ```ou ```@RestController ``` - met en place toute la plombrie rest 
- ```@RequestMapping (path = "/tripping/lists") ```

Miracle de logger :heart: ```Logger log = LoggerFactory.getLogger(this.getClass());```

# Repo
```Optional<ListeEntity> findByCategoryAndUser(String category, String user);```

---

# Postman 
![bg right w:500 h:400](images\postmanCollection.png) 
![ w:200 h:200](images\postman3.jpg) 

Miracle de Postman :heart:



---

# Défauts ![w:90 h:70](images\flat-clock-icon.png)
![bg right w:320 h:500](images\jokeProd.jpeg) 
* Je n'ai pas de tests unitaires ni d'intégrations 
* J'aurais voulu utilisé : 

    * Zull 
    * Eureka 
    * Config Serveur 
    * Swagger

---
# Angular 
![bg right w:450 h:300](images\Angular.png) 

* ![w:120 h:50](images\Google.png) 
* Open source 
* Typescript

---
# Commandes utiles 
- ```ng new my-trip-test``` - création d'une nouvelle appli angular4+
- ```ng g component lists```- génération d'un nouveau composant
- ```ng g service lists```- génération d'un nouveau service
- ```ng serve -o```- build en mémoire + démarrage serveur de test
- ```ng build --prod```- construction du dossier dist (pour déploiement)
- [Angular material -ex : Card](https://material.angular.io/components/card/examples) :heart:


---
# Liaison entre vue et modèle 
- ```<input matInput type="text" [(ngModel)]="city">``` 
Liaison dans les 2 sens (lecture/écriture)

- ```<mat-card-content>{{f.date | date:'fullDate'}}<mat-card-content>``` 
Liaison dans 1 sens (affichage) [(Angular - dates)](https://www.angularjswiki.com/angular/angular-date-pipe-formatting-date-times-in-angular-with-examples/) 

- ```<button (click)="onFetchforecastByCityName()"> Search</button> ```

---

# Principales directives prédéfinies
### *ngFor
    <mat-card *ngFor="let f of forecast ">
### *ngIf
        <div *ngIf="i.inBag ; else elseBlock">
            <mat-checkbox type="checkbox" [(ngModel)]="i.inBag" checked> <mat-checkbox>
        </div>
        <ng-template #elseBlock>
            <mat-checkbox id="checkBoxInBag" type="checkbox" [(ngModel)]="i.inBag"></mat-checkbox>
        </ng-template>

---
# Communication entre angular et NodeJs / Spring 

    export class WeatherService {

        constructor(private http: HttpClient) { }

        baseURL = 'https://tripping-nodejs.herokuapp.com' 
        
        public fetchWeatherforecastByCityname(cityname: String) :Observable<SingleWeatherforecast[]> {
            let url = `${this.baseURL}`+`/weather-api/public/forecastbycity/`+`${cityname}`; 
            console.log("In service, name = "+cityname+ "url= "+ url);
            return this.http.get<SingleWeatherforecast[]>(url);
        }
    }

---

# Node.js

``` ex: https://tripping-nodejs.herokuapp.com/weather-api/public/forecastbycity/paris```
<!-- ![bg right w:100 h:250](images\nodejs-logo-600x300.png)  -->

    apiRouter.route('/weather-api/public/forecastbycity/:cityname')
    .get(async function(req, res, next){
	var cityname = req.params.cityname; 
	const url = "http://api.openweathermap.org/data/2.5/forecast?q="+cityname+"&appid="+WEATHER_API_KEY+"&units=metric";
	const response = await fetch(url);
	const json = await response.json();
	const count = json.cnt;
	const forecastPretty = []; 
	for(i=0; i<count; i+=8){
		const weather = {
			city: json.city.name,
			date: json.list[i].dt_txt,
			temp: json.list[i].main.temp,
			feels_like: json.list[i].main.feels_like, 
			icon: json.list[i].weather[0].icon
		}
		forecastPretty.push(weather);
	}
	res.send(forecastPretty);		
    })

---

# Heroku  :heart:

![bg right w:500 h:250](images\heroku.png) 

[Heroku Dashboard](https://dashboard.heroku.com/apps)

Tripping App : https://tripping-angular.herokuapp.com/



---

# Git  :heart:
![bg right w:400 h:250](images\gits.jpg) 

[SofiaVP/tripping-nodejs-hub](https://github.com/SofiaVP/tripping-nodejs-hub)

[SofiaVP/tripping-MS-user-hub](https://github.com/SofiaVP/tripping-MS-user-hub)

[SofiaVP/tripping-MS-list-hub](https://github.com/SofiaVP/tripping-MS-list-hub)

[SofiaVP/tripping-angular-hub](https://github.com/SofiaVP/tripping-angular-hub)

---

# Evolution front 1/4

![w:1060 h:400](images\evolution1.png) 

---

# Evolution front 2/4

![w:1060 h:400](images\evolution2.png) 

---

# Evolution front 3/4

![w:1060 h:400](images\evolution3.png.jpg) 

---

# Evolution front 4/4

![w:1060 h:400](images\evolution4.png) 


---
# Petite demo ? ...
## Petite demo !

(elle sera plus au point vendredi)


[Tripping (localhost)](http://localhost:4200/tripping-register)

---

# Bilan
### Fiertés 
- Dark mode 
- J'ai appris beaucoup de choses :)
![bg right w:400 h:250](images\goodjobme.jpg) 
### Regrets  
- Pas de Docker 
- Pas de Tests  
- Front à revoir 
- Microservice todo à faire 
![bg vertical w:400 h:250](images\shame.jpg) 



--- 

## Dificultés rencontrés au cours du projet

- Difficultés à se lancer (API) 
- Modélisation difficil pcq'il y a plusieurs niveau (code, architechture etc...)  
- Images dans assets 
- Perte de spring  (D:\Project_Tripping_LeBon\WS_Eclipse_Trip\.metadata\.plugins\org.eclipse.core.resources\.history\95)
- Gestion du temps et du stress

--- 

Le groupe est toujours aussi soudé ce qui est super cool ! :heart:

# Merci pour votre attention ! 

