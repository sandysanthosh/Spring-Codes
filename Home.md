Welcome to the Spring-JBDC wiki!

`* https://goo.gl/Hq634n

https://github.com/bh5k/ride_tracker

https://app.pluralsight.com/player?name=building-applications-spring-jdbc-m2&clip=9&course=building-applications-spring-jdbc&author=bryan-hansen

->pox.xml
->add depencies
->Maven Dependencies
->Dependencies

RestControllerTest{

@Test(timeout=3000)
{
public void testCreateRide()
{
RestTemplate restemplate=new RestTemplate();

Ride ride=new Ride();
ride.setName("Santhosh");
ride.setDuration(35);

restTemplate.put("http:localhost:8080/ride_tracker/ride",ride);  // put method
}



RideController.java:

@Controller
public class RideController
{
@Autowired
private RideService rideService;

@RequestMapping(value="/ride",method=RequestMethod.PUT)   //put method
public @Responsebody Ride createRide(@RequestBody Ride ride)
{
return rideService;
}
}

update method:

public Ride updateRide(Ride ride){
jdbcTemplate.update("update ride set name=?, duration=? where id=?)
ride.getName(),ride.getDuration,ride.getId());
return ride;
}

Batch update:

public Ride updateRide(Ride ride){
jdbcTemplate.batchUpdate(
"update ride set name=? where id=? , pairs);
}

delete:

public Ride deleteRide(Ride ride){
jdbcTemplate.update("delete from ride where id=? , id);
}

NamedParameterJdbcTemplate namedTemplate=new
NamedParameterJdbcTemplate(jdbcTemplate);

map<String,object> paraMap=new map<String,object>();
paraMap.put("id",id);
namedTemplate.update("delete from ride where id= :id",paraMap);
}


select statement:

selectAll
RowMapper
CreateRead
modify create

List<ride> rides=jdbcTemplate.query("select * from ride",new RowMapper<Ride>()){

public Ride mapRow(ResultSet rd,int rowNum) throws SQLException{

Ride ride=new Ride();
ride.setId(rs.getId("id"));
ride.setName(rs.getId("name"));
ride.setDuration(rs.getId("duration"));
return ride;
}
});


create and update  -> restTemplate.put

create and update -> restTemplate.post

RowMapper->map the object anonyms inner class

RestControllerTest{

@Test(timeout=3000)
{
public void testCreateRide()
{
RestTemplate restemplate=new RestTemplate();

Ride ride=new Ride();
ride.setName("Santhosh");
ride.setDuration(35);

restTemplate.post("http:localhost:8080/ride_tracker/ride",ride);  // difference here
}


controller:


@Controller
public class RideController
{
@Autowired
private RideService rideService;

@RequestMapping(value="/ride",method=RequestMethod.POST)      //difference post
public @Responsebody Ride createRide(@RequestBody Ride ride)
{
return rideService;
}
}


pojo:

->id,name,duration



`
