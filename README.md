# Observe

You can find our web server at http://semanticweb.cs.vu.nl/observe.

The schema of observe and that of the vocabularies it depends on are stored in separate graphs:

http://semanticweb.cs.vu.nl/observe/browse/list_graphs.

The demonstration scripts (in Prolog) are on the server too:

http://semanticweb.cs.vu.nl/observe/swish/p/Spreadsheet_to_observations.pl
http://semanticweb.cs.vu.nl/observe/swish/p/Define_context.pl
http://semanticweb.cs.vu.nl/observe/swish/p/Context_to_source_observations.pl
http://semanticweb.cs.vu.nl/observe/swish/p/Collect_context_profile.pl
http://semanticweb.cs.vu.nl/observe/swish/p/Open_data_for_context_profile.pl
http://semanticweb.cs.vu.nl/observe/swish/p/Content_matching.pl. 

If the (collaborative) SWI-Prolog editor appeals to you, have a look at http://swish.swi-prolog.org.

Documentation for the above scripts:

Process spreadheets with observations:

Access: http://semanticweb.cs.vu.nl/observe/swish/p/Spreadsheet_to_observations.pl
Input: no input, but the script expects a spreadsheet ‘data_demo_observe.csv’ uploaded to the server (only admin can do this) with rows like this:
Enschede,52.2167,6.9,30,2015-07-15T09:00:00,20
Output: RDF triples in Data Cube, i.e. defined qb:Observation instances. 
Syntax: main.
Example: main.
Future work: Make the methods more generic. All properties are currently pre-defined (temperature, date time, location, lat/long, etc), so methods could be defined to declare attributes with any name and with values of any kind.

Define contexts:

Access: hhttp://semanticweb.cs.vu.nl/observe/swish/p/Define_context.pl
Input: details about the context, i.e. a time (in sxd:dateTime notation) and location (in lat and long) corresponding to the screen position. 
Output: RDF triples defining the context (with a URI as ) and the properties refTime and refArea
Dependencies, prerequisites: none.
Syntax: define_context(dateTime, lat, long).
Example: define_context('2015-07-15T09:00:00', 50.000, 5.000).  

Connect contexts to source observations:  

Access: http://semanticweb.cs.vu.nl/observe/swish/p/Context_to_source_observations.pl
Input: maximum allowed time difference in seconds between observations and contexts for the former to be relevant to the context.
Dependencies, prerequisites: requires defined instances of dq:Observation and observe:Context
Output: RDF triples relating all ob:Context instances to all dq:Observation instances with the same lat/long and a difference in refTime of at most [MaxDelayInSeconds], through predicate observe:source
Syntax: connect_contexts_to_sources(MaxDelayInSeconds).
Example: connect_contexts_to_sources(300).
Future work: Currently the observation and the context need the same lat/long as their reference area. Script Context_to_source_observations.pl contains some starting points for more advanced refArea comparisons.  

Collect the (first part of the) context profile based on observations: 

Access: http://semanticweb.cs.vu.nl/observe/swish/p/Collect_context_profile.pl
Input: none, although the script depends on (pre-defined) context attribute-value definitions, consisting of the name of the ContextAttributeValue (e.g. sunshine-high), the name of the measurement it refers to (e.g., sunshine), and the lower and upper bound float numbers that are the criteria for the measurement to get this context attribute-value (e.g., between 0 and 0.50). 
Output:  RDF triples with all the properties of a given context. Method all_contextprofiles fills out the contextattribute values for all contexts defined, based on the Observation instances that are detected in the previous step as a valid source for the context profile.
Syntax: 
all_contextprofiles. This collects the context profiles.
define_contextattributevalue(Name, PropertyName, Lower, Upper). To define a new context attribute-value pair, if needed. 
Examples:  
all_contextprofiles.
define_contextattributevalue('crowdedness-low', 'crowdedness', 0, 0.09). 
Future work: define the context attribute values outside the Prolog environment, for instance via a comma-separated file in the same folder as the source observations.

Add to the context profile data from the web of Open Data:

Access: http://linkedpolitics.eculture3.labs.vu.nl/swish/p/Contextprofile_from_open_data.pl 
Input: a type of venue the occurrence of which is queried, as a Linked Geodata category; a lat and long coordinate around which it is queried; a radius in metres around which to look for such venues
Output: the presence (true/false) of venues of the specified type within the square region that spans the specified number of metres around the specified lat/long coordinate, if desired also the name of each venue. Add a 'count' operation to the query from the 'solutions' tab to retrieve the number of venues instead.
Syntax: 
venues_of_type(VenueType, Lat, Long, DistanceInMetres). 
venues_of_type(VenueType, Lat, Long, DistanceInM, VenueName).
Example: nr_of_venues_of_type('http://linkedgeodata.org/ontology/School', 6.917, 52.210, 500). 
Future work: 1) make qualitative context profiles based on ratios of different types of venues. For instance, mark an area with more museums than schools as a tourist area. 2) consider downloading the relevant set of the Linked Geodata database for reliability.

Match content items to contexts:

http://semanticweb.cs.vu.nl/observe/swish/p/Content_matching.pl
Input: none, but requires objects of type observe:ContentItem with ob:suitable-for relations in the RDF store.
Output: RDF triples relating all ob:Context instances to all ob:ContentItem instances that are suitable for that context, through property ob:suitable-for. By ‘suitable for a context’, we mean suitable for all the attribute-value pairs defined for the context.
Syntax: match_all_contexts.
Example: match_all_contexts.
