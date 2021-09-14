# Piero Amtrak API Documentation
This markdown file will guide you through the objects and endpoints from the API you can call/interact with.

## Objects
I have no idea what the proper name for these are, but they're like structs. There are three of them, `station`, `stationMin`, and `trainData`. These are all pretty self-explanitory of what each variable holds.

```ts
interface station {
	trainNum: number; //number of the train station is from
	code: string; //code of station
	tz: string; //timezone of station (EST, EDT, CST, CDT, PST, or PDT)
	bus: boolean; //true if bus at stop
	schArr: Date; //scheduled arrival at station
	schDep: Date; //scheduled departure from station
	schMnt: string; //variable from amtrak, not sure use of but could be related to any maintnence the train will go through at this station
	autoArr: boolean; //has the train arrived at this station already?
	autoDep: boolean; //has the train departed from this station already?
	postArr?: Date; //actual arrival at station
	postDep?: Date; //actual departure from station
	postCmnt?: string; //how late it departed in english
	estArr?: Date; //estimated arrival at station
	estDep?: Date; //estimated departure from station
	estArrCmnt?: string; //how early/late train will be in english
	estDepCmnt?: string; //how early/late train will be in english
}
```

```ts
interface stationMin {
	trainNum: number; //number of the train station is from
	schArr: Date; //scheduled arrival at station
	schDep: Date; //scheduled departure from station
	postArr?: Date; //actual arrival at station
	postDep?: Date; //actual departure from station
	postCmnt?: string; //how late it departed in english
	estArr?: Date; //estimated arrival at station
	estDep?: Date; //estimated departure from station
	estArrCmnt?: string; //how early/late train will be in english
	estDepCmnt?: string; //how early/late train will be in english
}
```

```ts
interface trainData {
	routeName: string; //name of the route
	trainNum: number; //train number
	coordinates: number[]; //coordinates in lat, lon
	lat: number; //current latitude position of train
	lon: number; //current longitude position of train
	heading: string; //heading of the train in N, NE, E, SE, S, etc.
	velocity: number;
	lastValTS: Date; //Date object which train was last updated
	lastArr: Date; //Date object which train arrived at final destination, null if still uncompleted
	trainState: string; //state of the train ("Predeparture", "Active", or "Completed")
	statusMsg: string; //status of the train (" " if normal, "SERVICE DISRUPTION" if the obvious has happened)
	serviceDisruption: boolean; //true if a service disruption
	eventCode: string; //upcoming or current stop
	destCode: string; //final destination
	origCode: string; //origin station
	originTZ: string; //timezone of origin station (EST, EDT, CST, CDT, PST, or PDT)
	origSchDep: Date; //scheduled original departure for train
	aliases: number[]; //train numbers which also refer to this train
	updatedAt: Date; //the time this data was retrieved from the server
	stations: station[]; //array of station objects
};
```

## Endpoints
There are 6 endpoints on this API, each making your life tracking Amtrak trains programmatically easier. The endpoint to use for these it https://api.amtrak.piemadd.com/v1, which can be used in your application or even add Amtrak API support to other libraries. Everything is returned as JSON.

[`/trains/keys`](https://api.amtrak.piemadd.com/v1/trains/keys) - Returns a key-value dictionary of train numbers and their corresponding name.

[`/trains/{trainNum}`](https://api.amtrak.piemadd.com/v1/trains/{trainNum}) - Returns an array of `trainData` objects with every object being from a train posessing the given train number (trainNum).

[`/trains`](https://api.amtrak.piemadd.com/v1/trains) - Returns object where the keys are the number to an Amtrak train and the values are what `fetchTrain()` would return, being an array of `trainData` objects.

[`/stations/{stationCode}`](https://api.amtrak.piemadd.com/v1/stations) - Returns a list of `stationMin` objects correlating to the station code passed, with each object being related to a train arriving within the next few hours or having left a few hours back. 

[`/stations`](https://api.amtrak.piemadd.com/v1/trains/{stationCode}) - Returns an object where the keys are station codes and the objects being what `fetchStation()` would return.