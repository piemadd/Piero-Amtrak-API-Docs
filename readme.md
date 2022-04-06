# Piero Amtrak API

This API makes it super easy to programmatically interact with Amtrak's train tracking libray from any language supporting CURL requests and JSON. The library is written in Javascript using Express.js and [Amtrak.js](https://npmjs.com/package/amtrak), the latter of which is my own library. This markdown file will guide you through the objects and endpoints from the API you can call/interact with.

## Objects
I have no idea what the proper name for these are, but they're like structs. There are three of them, `station`, `stationMin`, and `trainData`. These are all pretty self-explanitory of what each variable holds.

```ts
let trainData = {
	routeName: string; //name of the route
	trainNum: number; //train number
	objectID: numner; //a unique identifier for this train.
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

```ts
let station = {
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
let stationMin = {
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

## Endpoints
There are 6 endpoints on this API, each making your life tracking Amtrak trains programmatically easier. The endpoint to use for these it https://api.amtraker.com, which can be used in your application or even add Amtrak API support to other libraries. Everything is returned as JSON.

[`/v1/trains/keys`](https://api.amtraker.com/v1/trains/keys) - Returns a key-value dictionary of train numbers and their corresponding name.

[`/v1/trains/{trainNum}`](https://api.amtraker.com/v1/trains/{trainNum}) - Returns an array of `trainData` objects with every object being from a train posessing the given train number (trainNum).

[`/v1/trains`](https://api.amtraker.comv1/trains) - Returns object where the keys are the number to an Amtrak train and the values are what `fetchTrain()` would return, being an array of `trainData` objects.

[`/v1/stations/keys`](https://api.amtraker.com/v1/stations/keys) - Returns a key-value dictionary of station codes and their corresponding name.

[`/v1/stations/{stationCode}`](https://api.amtraker.com/v1/stations/{stationCode}) - Returns a list of `stationMin` objects correlating to the station code passed, with each object being related to a train arriving within the next few hours or having left a few hours back. 

[`/v1/stations`](https://api.amtraker.com/v1/stations/) - Returns an object where the keys are station codes and the objects being what `fetchStation()` would return.

[`/v2/oembed?url={amtrakerUrl}`](https://api.amtraker.com/v2/oembed?url={amtrakerUrl}) - Returns oembed data based on the `amtrakerUrl` provided. A valid Amtraker URL is one which looks something like this `https://amtraker.com/view.html?train=123456`. The number which `train` is equal to is the objectID of a train. To test your code, feel free to use the above `/v1/trains` endpoint to get a list of all trains, in which you will find plenty of valid objectIDs. 

There are a list of valid domains, which include:
- https://amtraker.com
- https://www.amtraker.com
- https://amtrak.cc
- https://www.amtrak.cc
- https://beta.amtrak.cc
- https://whereismytrain.us
- https://www.whereismytrain.us
- https://whereismyfuckingtrain.com
- https://www.whereismyfuckingtrain.com

The default response is in the JSON oEmbed format, though if you add `xml=true` onto your request, you will receive an xml response. 

Here is a list of example oEmbed requests:
- https://api.amtraker.com/v2/oembed?url=https://amtraker.com/view.html?train=23085
- https://api.amtraker.com/v2/oembed?url=https://whereismytain.usview.html?train=23085&xml=true

