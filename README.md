# AIRLINES BOOKING DATASET
I will test my advanced SQL knowledge in this portfolio by analyzing airline booking data. The goal is to demonstrate data cleaning, aggregation, conditional logic using CASE statements, and subqueries to derive actionable insights from the dataset. I asked ChatGPT to act as my manager and give me instruction on what to write on MySQL.

## THE DATASET
The dataset contains 50,000 data of customers booking.

Link: [Airlines Booking Dataset](customer_booking.csv)

Below is how the dataset looks like on MySQL
![the_dataset](the_dataset.png)

# PROJECT OBJECTIVE
The goal of this project is to analyze airline booking patterns to uncover customer behavior and identify business opportunities. Using SQL, I explored passenger segmentation, service preferences, and booking trends to generate actionable insights for decision-making.

# 20 ADVANCED QUERIES
The advanced queries in this portfolio are CASE, subqueries, ranking and windonw function, aggregation and grouping, derived metrics, date/time analysis

## CASE

### 1. Segment our passengers into travel groups (Solo, Small, Large) and tell me how many bookings fall into each segment.

#### The Query
```
SELECT
  CASE
    WHEN num_passengers > 6 THEN 'Large Group'
        WHEN num_passengers BETWEEN 2 AND 5 THEN 'Small Group'
        ELSE 'Solo'
	END AS travel_group, 
    COUNT(num_passengers) AS total_booking
FROM airlines_booking
GROUP BY travel_group,
	CASE
		WHEN num_passengers > 6 THEN 'Large Group'
        WHEN num_passengers BETWEEN 2 AND 5 THEN 'Small Group'
        ELSE 'Solo'
	END
ORDER BY total_booking DESC;
```

#### Insight
The Result
This query will show the total number of bookings from customers. The numbers have been categorized in large group, small group, and solo. From this query, we are able to see how many of our customers travel in large, small group or solo.

Real-world Analysis
Out of 50,000 customers:
- Only 269 customers (0.5%) booked in large groups.
- 36% of customers booked in small groups.
- 63% were solo travelers.

The number of large-group bookings is significantly lower compared to the other two categories. This suggests that the airline’s customer mostly consists of solo and small-group travelers. Therefore, marketing and promotional strategies could focus on strengthening these two segments. As for instance, offering single-seat discounts or small-group bundles to increase overall booking volume.

##### Screenshot of the Query
![case_1](case_1.png)

### 2. Identify bookings where passengers requested extras (extra baggage, preferred seat, or in-flight meals) and classify each booking as “High Extras”, “Medium Extras”, or “No Extras” based on how many extras were requested. Show the total bookings in each category.

#### The Query
```
SELECT
  CASE
		WHEN (wants_extra_baggage + wants_preferred_seat + wants_in_flight_meals) > 3 THEN 'high request'
		WHEN (wants_extra_baggage + wants_preferred_seat + wants_in_flight_meals) BETWEEN 1 AND 2 THEN 'normal request'
		ELSE 'no request'
	END AS extra_request,
	COUNT(*) AS total_booking
FROM airlines_booking
GROUP BY
	CASE
		WHEN (wants_extra_baggage + wants_preferred_seat + wants_in_flight_meals) > 3 THEN 'high request'
		WHEN (wants_extra_baggage + wants_preferred_seat + wants_in_flight_meals) BETWEEN 1 AND 2 THEN 'normal request'
		ELSE 'no request'
	END
ORDER BY total_booking DESC;
```

#### Insight
The Result
This query shows the total number of bookings that include any extra request, such as extra baggage, preferred seat, or in-flight meals.

Real-world Analysis
- 38.45% of customers do not purchase any of our extra service. Special promotions on our extra services have the potential to boost our sales (decrease the percentage) on this. As for example, certain period of time, we should offer special discount if our customers take at least two of our special service. Pay full for extra baggage and get 10% off for preferred seat price or in flight meals.
- The rest of our customers have bought at least 1 extra service from us. These campaigns could also positively impact customers who already purchase extras.

#### Screenshot of the Query
![case_2](case_2.png)

## SUBQUERY

### 1. Find all bookings where the length of stay is longer than the average stay, and show how many total bookings fall under this category and also the average of length of stay

#### The Query
```
SELECT 	ID, 
		num_passengers,
        length_of_stay,
        trip_type, 
		(SELECT COUNT(*) FROM airlines_booking WHERE length_of_stay > (SELECT AVG(length_of_stay) FROM airlines_booking)) AS total_booking,
        (SELECT ROUND(AVG(length_of_stay), 0) FROM airlines_booking) AS avg_length_of_stay
FROM airlines_booking
WHERE length_of_stay > (SELECT AVG(length_of_stay) FROM airlines_booking)
ORDER BY length_of_stay DESC;
```

#### Insight
The Result
This query identifies all bookings where the trip’s length of stay is above the average across all customers. It also displays the overall number of bookings exceeding that average and the calculated average length of stay for context. Besides that, the result also shows the average of length of stay.

Real-world Analysis
- The data shows that 15,966 customers (around 32% of total bookings) have a length of stay longer than the average of 5 days. The longest customer stays up to 700 days.
- Customers with longer stays are likely to have extended trips, vacations, or business stays, which often correlate with higher spending on add-ons like baggage or in-flight meals.
- Airlines can target this segment with loyalty rewards, upgrade offers, or extended-stay partnerships (e.g., hotel collaborations, car rental).
- Although these customers might not travel frequently, they bring higher revenue per trip.

#### Screenshot of the Query
![case_11](case_11.png)

### 2. How many Internet bookings have more passengers than the average number of passengers in Mobile bookings?

#### The Query
```
SELECT 
    ID,
    num_passengers,
    sales_channel,
    (SELECT ROUND(AVG(num_passengers), 0)
     FROM airlines_booking
     WHERE sales_channel = 'Mobile') AS avg_mobile_passengers,
     (SELECT SUM(num_passengers) FROM airlines_booking WHERE sales_channel = 'internet') AS internet_booking,
     (SELECT SUM(num_passengers) FROM airlines_booking WHERE sales_channel = 'mobile') AS mobile_booking
FROM airlines_booking
WHERE sales_channel = 'Internet'
  AND num_passengers > (
        SELECT AVG(num_passengers)
        FROM airlines_booking
        WHERE sales_channel = 'Mobile'
  )
ORDER BY num_passengers DESC;
```

#### Insight
The Result
The query identifies Internet bookings that exceed the average group size of Mobile bookings and provides the total passengers for each channel.

Real-world Analysis
- Average passengers per Mobile booking: 2
- Total passengers via Mobile: 8,900
- Total passengers via Internet: 70,662
- Only 2 Internet bookings exceed the average Mobile booking size, indicating that larger groups predominantly book via Mobile.
- Actionable Insight: Airlines could target Mobile users with group booking promotions or discounts to attract more large-group customers and increase overall revenue.

#### Screenshot of the Query
![subquery_2](subquery_2.png)
