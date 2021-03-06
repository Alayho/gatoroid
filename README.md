# Gatoroid

Gatoroid is a way to store analytics using the powerful features of MongoDB for scalability

Leveraging mongoid, Gatoroid is a way to increment, decrement, add to, and reset counters in MongoDB.  The data is stored using an hourly grain in a utc allowing for easy analytics for on just about anything.

## Using Gatoroid to store analytics information
Simply create a class that includes Mongoid::Gator

```ruby
class SiteStats
  include Mongoid::Gator
  field :siteid
  gator_field :visits
end
```

Use the class to increment the counter for visits to the site.

```ruby
@site_stats = SiteStats.new
@site_stats.visits.inc(siteid: 100)
```


You can also add to the count using the add method.  The following adds 10 visits to tomorrow.

```ruby
@site_stats.visits.add(10, date: Today.now + 1.day)
```

The data will be stored in MongoDB using seconds since the epoch in hours

```javascript
{
  "_id" : ObjectId("4f85b40f2a2c4e4d9709eaf9"),
  "date" : 1334160000,
  "siteid" : 100,
  "visits" : 1
}
```

You can view the total visits to a site by using some built in functions.  For example:

```ruby
@site_stats = SiteStats.new()
@site_stats.visits.today(siteid: 100)  # returns total for today as integer
@site_stats.visits.yesterday(siteid: 100) # returns total for yesterday as integer
```


To get a list of visits to for a range, use the range method.

```ruby
@site_stats = SiteStats.new()
@site_stats.visits.range(Time.now..Time.now + 1.day,Mongoid::Gator::Readers::DAY, siteid: 100)
```

You can change the grain on which you would like to return the data using

```ruby
Mongoid::Gator::Readers::HOUR
Mongoid::Gator::Readers::DAY
Mongoid::Gator::Readers::MONTH
```
