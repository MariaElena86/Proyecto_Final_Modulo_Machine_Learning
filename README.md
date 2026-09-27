# 🏨 Predicción de Cancelación de Reservas Hoteleras
##  Máster en Data Science & IA · Evolve Academy

**Dataset** Hotel Booking Demand (Kaggle: `jessemostipak/hotel-booking-demand`.)
**Objetivo** ⭐  Predecir si una reserva será cancelada en el momento en que se realiza. 
**Autor** Maria Elena Carralero
**Video** [https://www.loom.com/share/11990bba94bf4a95aa666d3580a0c789](https://www.loom.com/share/11990bba94bf4a95aa666d3580a0c789)

## Carga y revisión inicial de los datos
- Crear el DataFrame
- Revisar la estructura(Total de registro, columnas)
- Describir variables
- Revisar valores faltantes
- Revisar tipos de datos

## Descripción de las Variables

| Variable | Tipo | Descripción |
|---|---|---|
| `hotel` | Categórica | Tipo de hotel: H1 = Resort Hotel, H2 = City Hotel |
| `is_canceled` | Binaria (target) | 1 = reserva cancelada, 0 = no cancelada |
| `lead_time` | Numérica | Días entre la fecha de reserva y la fecha de llegada |
| `arrival_date_year` | Numérica | Año de llegada |
| `arrival_date_month` | Categórica | Mes de llegada |
| `arrival_date_week_number` | Numérica | Semana del año de llegada |
| `arrival_date_day_of_month` | Numérica | Día del mes de llegada |
| `stays_in_weekend_nights` | Numérica | Noches de fin de semana (sáb/dom) reservadas o estancia real |
| `stays_in_week_nights` | Numérica | Noches entre semana (lun–vie) reservadas o estancia real |
| `adults` | Numérica | Número de adultos |
| `children` | Numérica | Número de niños |
| `babies` | Numérica | Número de bebés |
| `meal` | Categórica | Régimen alimenticio: SC/Undefined = sin paquete; BB = solo desayuno; HB = media pensión; FB = pensión completa |
| `country` | Categórica | País de origen del cliente (formato ISO 3155-3:2013) |
| `market_segment` | Categórica | Segmento de mercado. TA = Travel Agents, TO = Tour Operators |
| `distribution_channel` | Categórica | Canal de distribución de la reserva. TA = Travel Agents, TO = Tour Operators |
| `is_repeated_guest` | Binaria | 1 = cliente repetidor, 0 = primera vez |
| `previous_cancellations` | Numérica | Número de cancelaciones previas del cliente |
| `previous_bookings_not_canceled` | Numérica | Número de reservas previas no canceladas del cliente |
| `reserved_room_type` | Categórica | Código del tipo de habitación reservada (anonimizado) |
| `assigned_room_type` | Categórica | Código del tipo de habitación asignada (puede diferir de la reservada por overbooking o petición del cliente) |
| `booking_changes` | Numérica | Número de modificaciones realizadas sobre la reserva hasta check-in o cancelación |
| `deposit_type` | Categórica | Depósito realizado: No Deposit = sin depósito; Non Refund = depósito por el total de la estancia; Refundable = depósito parcial reembolsable |
| `agent` | Categórica | ID de la agencia de viajes que realizó la reserva (anonimizado) |
| `company` | Categórica | ID de la empresa responsable de la reserva o del pago (anonimizado) |
| `days_in_waiting_list` | Numérica | Días que la reserva estuvo en lista de espera antes de confirmarse |
| `customer_type` | Categórica | Tipo de reserva: Contract = con contrato; Group = grupo; Transient = individual sin contrato; Transient-party = individual asociado a otra reserva transient |
| `adr` | Numérica | Average Daily Rate: precio medio por noche (€) |
| `required_car_parking_spaces` | Numérica | Plazas de aparcamiento solicitadas por el cliente |
| `total_of_special_requests` | Numérica | Número de peticiones especiales del cliente (ej. cama doble, piso alto) |
| `reservation_status` | Categórica |  — último estado: Canceled / Check-Out / No-Show |
| `reservation_status_date` | Fecha |  — fecha en que se estableció el último estado |
---
## Análisis y Exploracion de los Datos (EDA)
Distribución de la variable objetivo (`is_canceled`): 
 - City Hotel mantiene un volumen elevado de cancelaciones durante todo el año, con especial concentración entre mayo y agosto, mientras que Resort Hotel presenta una estacionalidad más marcada, alcanzando sus máximos en julio y agosto.
  
Análisis de ingresos:
 - City Hotel concentra el mayor impacto económico potencial de las cancelaciones, con 10,89 M€ asociados a reservas canceladas, frente a 5,84 M€ en Resort Hotel.  
  
Análisis del inpacto de inpacto tiene el `lead_time`: 
 - Las reservas realizadas con poca antelación (0–7 días) registran la tasa de cancelación más baja, mientras que el riesgo aumenta drásticamente en las reservas realizadas a medio y largo plazo (90–180 días, 181–365 días y +365 días)
  
Análisis del inpacto de `distribution_channel`: 
 - El canal TA/TO concentra el mayor volumen de reservas, con 97.870 reservas, y presenta una tasa de cancelación del 41,0%, muy superior a la observada en los canales Direct (17,5%), Corporate (22,1%) y GDS (19,2%). El canal Undefined presenta una tasa de 80% pero no es muy orinetativo ya que solo tiene 5 reservas y 4 canseladas.
  
Análisis del inpacto de `adr` la cancelacion de la reserva: 
 - La diferencia es de unos 5 €, el adr tiene valores extremos (-6.38 y 5400), por lo que la media puede estar afectada por esos valores.


---
## Tratamiento de nulos, outliers y data leakage

#### Valores Null
- `company` con un 94,31 % de null → eliminar la variable, no es de gran aporte para la prediccopn de cancelacion.
- `agent` con un 13,69 % de null → imputar con la media.
- `country` con un 0,41 % de null → imputar con "Desconocido".
- `children` con solo 4 valores null -> imputar con 0 porque la mediana es 0

#### Valores extremos(outliers)
- `lead_time`: Presenta valores altos, pero son valores posibles en la practiva.
- `stays_in_week_nights`: Presenta valores altos, pero son valores posibles en la practiva.
- `adr`: presenta un mínimo de -6,38 y un máximo de 5.400, siendo especialmente relevante por la presencia de un valor negativo y un máximo muy alejado de la mediana (94,58).
- Un ADR negativo no es posible en operaciones hoteleras reales.Este valor representa un error de registro y se debe eliminar.
- Un ADR de 5400 € por noche es extremadamente improbable para un City Hotel.
Este valor debe ser un error de carga y es mejor eliminar.

- Para las variables `adults` y `days_in_waiting_list` el metodo IQR no es el mejor, ya que son variables casi constante
que presentan algunos extremos que pueden ser valores reales no necesariamente errores.

#### ⚠️Variables que contienen información que solo se conoce después de la reserva (data leakage)
- `reservation_status`: Indica el estado final de la reserva (`Canceled`, `Check-Out`, `No-Show`) - Eliminar variable
- `reservation_status_date`: Indica cuándo se estableció el estado final de la reserva. - Eliminar variable
- `assigned_room_type`: La habitación es asignada al realizar la reserva. - Eliminar variable

---
## Feature Engineering
- Crea la variable arrival_date para ordenar el dataset y asi dividir en train/test
- Se crea a partir de las variables(arrival_date_year, arrival_date_month,arrival_date_day_of_month)
---
## Train y Test

Seleccionar fecha de corte
- Se crearon pruebas de varias fechas para seleccionar el % mas optimo    
    - fecha_corte '2016-08-01' - Resultado(45%/55%)
    - fecha_corte '2016-10-01' - Resultado(53.76%/46.24%)
    - fecha_corte '2016-11-01' - Resultado(58.96%/41.04%)
    - fecha_corte '2016-12-01' - Resultado(62.69%/37.31%)

- La fecha corte seleccionada fue '2016-12-01'
StandardScaler + OneHotEncoder
- Transformar Variables categóricas → OneHotEncoder
- Transformar Variables numericas → StandardScaler

---
## Modelos 
- Regresión Logística - baseline
- Regresión Logística balanceada
- Decision Tree
- XGBoost
- Random Forest

### Optimización de hiperparámetros: GridSearch
- Random Forest presenta una precision de 0.8385, pero un recall de 0.5447, por lo que, aunque sus predicciones de cancelación son bastante precisas, no detecta una parte importante de las cancelaciones reales. Su AUC es de 0.8704.
- Se aplicará GridSearchCV para buscar una combinación de hiperparámetros que permita mejorar el rendimiento del modelo.

---
## Comparación de modelos Seleccion del modelo ganador

Una vez entrenados y evaluados los diferentes modelos, se comparan sus resultados utilizando las métricas definidas: AUC-ROC, Precision, Recall, F1-score y Accuracy.

El objetivo de esta comparación es identificar el modelo que ofrece un equilibrio adecuado entre la capacidad de discriminación y la detección de las reservas que finalmente son canceladas.

La tabla comparativa obtenida es la siguiente:

| Técnica                        |    AUC | Precision | Recall | F1-score | Accuracy |
| ------------------------------ | -----: | --------: | -----: | -------: | -------: |
| Logistic Regression - baseline | 0.8494 |    0.7218 | 0.6763 |   0.6983 |   0.7751 |
| Logistic Regression - balanced | 0.8494 |    0.6305 | 0.8175 |   0.7119 |   0.7454 |
| Decision Tree                  | 0.7113 |    0.6838 | 0.5895 |   0.6331 |   0.7371 |
| Random Forest                  | 0.8704 |    0.8385 | 0.5447 |   0.6604 |   0.7844 |
| XGBoost                        | 0.8656 |    0.8232 | 0.5589 |   0.6658 |   0.7841 |
| Random Forest - optimizado     | 0.8799 |    0.8379 | 0.5423 |   0.6585 |   0.7835 |

Dado que el objetivo del negocio es identificar reservas con alta probabilidad de cancelación para poder aplicar acciones preventivas, se da especial importancia al Recall de la clase 1, ya que mide la capacidad para detectar las cancelaciones reales.

El Random Forest optimizado obtiene el mayor AUC (0.8799), pero presenta un Recall de 0.5423. 
Por su parte, la Regresión Logística balanceada alcanza un Recall de 0.8175 y el F1-score más alto (0.7119), aunque su AUC es inferior (0.8494).

Por este motivo, se selecciona la Regresión Logística balanceada como modelo final, ya que permite detectar una mayor proporción de las cancelaciones reales y se ajusta mejor al objetivo de negocio.

---
## Interpretabilidad/Explicabilidad
- SHAP Summary Plot: Importancia global de las variables
- SHAP Waterfall Plot


#### Varibales con mayor riesgo de cancelación:
- `country_PRT`, `lead_time`,`previous_cancellations`, `deposit_type_Non_Refund`

#### Variables con menor probabilidad estimada de cancelación:
- `previous_bookings_not_canceled`,`required_car_parking_spaces`, `total_of_special_requests`

### SHAP Waterfall Plot
- Reserva 1: una con probabilidad de cancelación alta.
- Reserva 2: una con probabilidad de cancelación baja.

---
### Conclusión

##### El perfil con mayor riesgo de cancelación:
- La reserva con índice 65238 presenta una probabilidad estimada de cancelación del 99,89%.
- Los principales factores que contribuyen a aumentar el riesgo estimado son:
  - `lead_time`: presenta una contribución positiva importante, indicando que el valor de esta variable incrementa la predicción de cancelación.
  - `deposit_type_Non Refund`: contribuye positivamente a la predicción.
  - `deposit_type_No Deposit`: también presenta una contribución positiva en esta observación.
  - `country_PRT`: presenta una contribución positiva, aumentando el riesgo estimado por el modelo.

##### El perfil con menor riesgo de cancelación: 
- La reserva con índice 29045 presenta una probabilidad estimada de cancelación del 0,00%.
- En este caso, destacan varias contribuciones negativas que reducen fuertemente la predicción:
  - `required_car_parking_spaces`: presenta una contribución negativa especialmente elevada, siendo el factor con mayor impacto en esta predicción.
  - `is_repeated_guest`: contribuye a reducir el riesgo estimado.
  - `previous_bookings_not_canceled`: también presenta una contribución negativa, asociándose con una menor probabilidad estimada de cancelación.
 
  

