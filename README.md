# How it Works
Explanation of how the flight route search algorithm works!

**P.S.**: You didn't mention in GitHub [README.md](https://github.com/kiwicom/python-weekend-entry-task) what the limitation is in the number of stops between origin and destination OR any total travel time limitation. This algorithm will show all routes between origin and destination, which overlay time in **each stop** is more than one hour and less than 6 hours with no repeated stops (including origin).

**P.S. 2**: Unfortunately, I couldn't understand what "**bags_count**" is related to! SO I set "?" as the value of it.

**P.S. 3**: It is not clear what you mean by the "return" argument! But I think you mean a round trip! But still, you didn't mention in GitHub [README.md](https://github.com/kiwicom/python-weekend-entry-task) how the return flight should operate! You described it in just one example, which is for a trip with a non-stop flight as below. There is no other description or example!
```
python -m solution example/example0.csv RFZ WIW --bags=1 --return
```
> will perform a search RFZ -> WIW -> RFZ for flights which allow at least 1 piece of baggage.

But what if we have a flight route that has multiple stops? like flight route in **example3.csv** from **NNB** to **VVH** as follows: (NNB -> ZRW -> JBN -> EZO -> WUE -> WTN -> VVH)
```
{
  "flights": [
    {
      "flight_no": "YL366",
      "origin": "NNB",
      "destination": "ZRW",
      "departure": "2021-09-01T16:10:00",
      "arrival": "2021-09-01T19:10:00",
      "base_price": "47.0",
      "bag_price": "10",
      "bags_allowed": "2"
    },
    {
      "flight_no": "YL492",
      "origin": "ZRW",
      "destination": "JBN",
      "departure": "2021-09-01T21:25:00",
      "arrival": "2021-09-02T02:15:00",
      "base_price": "203.0",
      "bag_price": "10",
      "bags_allowed": "2"
    },
    {
      "flight_no": "YL471",
      "origin": "JBN",
      "destination": "EZO",
      "departure": "2021-09-02T04:55:00",
      "arrival": "2021-09-02T07:30:00",
      "base_price": "31.0",
      "bag_price": "10",
      "bags_allowed": "1"
    },
    {
      "flight_no": "JT755",
      "origin": "EZO",
      "destination": "WUE",
      "departure": "2021-09-02T11:10:00",
      "arrival": "2021-09-02T14:25:00",
      "base_price": "76.0",
      "bag_price": "11",
      "bags_allowed": "1"
    },
    {
      "flight_no": "YL562",
      "origin": "WUE",
      "destination": "WTN",
      "departure": "2021-09-02T17:30:00",
      "arrival": "2021-09-02T23:15:00",
      "base_price": "337.0",
      "bag_price": "10",
      "bags_allowed": "2"
    },
    {
      "flight_no": "YL244",
      "origin": "WTN",
      "destination": "VVH",
      "departure": "2021-09-03T04:05:00",
      "arrival": "2021-09-03T08:50:00",
      "base_price": "189.0",
      "bag_price": "10",
      "bags_allowed": "2"
    }
  ],
  "bags_allowed": "1",
  "bags_count": "?",
  "destination": "VVH",
  "origin": "NNB",
  "total_price": 944,
  "travel_time": "1 day, 16:40:00"
}
```
So is it possible that the return flight has multiple stops, too? in this solution I consider it as **YES**! and specify all possible routes from destination to origin in the "**return**" tag.

## Algorithm and Data Structures
There are two files, `solution.py` and `flight.py`, which `flight.py` contains the flight class in it, and `solution.py` gets the related arguments from CLI and calls the mentioned class with these arguments.

In the Flight class in the initiation __filter_origin function is executing.

Attributes:  
1. `csv` (attributes, public, String) set by the csv in class initiation
2. `origin` (attributes, public, String) set by the origin in class initiation
3. `destination` (attributes, public, String) set by the destination in class initiation
4. `bags` (attributes, public, int) set by the number of bags in class initiation - default is 0
5. `return_flight` (attributes, public, Boolean) set by the return value in class initiation - default is False
6. `return_flight_list` (attributes, public, list) will get the return flight trips (list of dictionaries)
7. `__json` (attributes, private, dictionary) will get the value of the converted CSV
8. `__marked_json` (attributes, private, dictionary) will get the marked trip's value.
9. `__column_name` (attributes, private, list) set by the list of the default values of CSV columns.
10. `result` (attributes, public, dictionary) set by all possible routes from A to B.

Functions:
1. `__filter_origin` (function, private, without argument, without return) is executing `__mark_flight_number` then create a dictionary which keys are origins and values are dictionaries of trips with related origins and assign it to `__filtered_origin` attribute!
2. `__mark_flight_number` (function, private, without argument, without return) is executing `__convert_csv_to_json` then mark each trip with a unique and increasing int number (which is zero based) and assign it to `__marked_json` attribute!
3. `__convert_csv_to_json` (function, private, without argument, without return) is opening the CSV file, converting it to a dictionary, and assigning it to `__json` attribute!
4. `__recursive_search` (function, private, with argument, return dictionary object) processes the possible routes between A to B recursively based on `__filtered_origin`, and return the dictionary!
arguments:
- `dest_list`: list of the current searched origins (list, without default value)
- `json_data`: a dictionary of the trips with the mentioned origin (dictionary, without default value)
- `current_dep_time`: departure time of the current loop of recursive (string, without default value)
5. `create_json` (function, public, without argument, return a list of dictionaries) is iterating `__nested_dict_iterator` then creating a list of dictionaries for requested JSON format based on the result attribute!
6. `__nested_dict_iterator` (function, private, with argument, without return) is iterating dictionary (nested dictionaries) which is passed by `dict_obj` argument!
- `dict_obj`: a dictionary of the trips with the route between mentioned origin and destination (dictionary "nested dictionaries", without default value)
7. `__return_list` (function, private, without argument, without return) is executing `Flight` class again for the return trip and assigning the final JSON to `return_flight_list` attribute!
8. `__filter_return_by_arrival_time` (function, private, with argument, with return) filter the result of the return trips based on time and return routes if the first return departure is after the final arrival.
- `arrival_time`: string timestamp of the current flight arrival time. (String, without default value)
9. `__time_differ` (function, private, with argument, with return) calculating between two dates and times.
- `date_1`: First date and time (string, without default value)
- `date_2`: Second date and time (string, without default value)
- `p`: set True if return interval needed as Integer. (boolean, default value is False)

# How to Run
