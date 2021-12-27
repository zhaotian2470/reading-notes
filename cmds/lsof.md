# useful commands
```
lsof -i -n -P # list internet connections
```

# useful parameters
-i [i]: selects  the  listing  of files any of whose Internet address matches the address specified in i.  If no address is specified, this option selects the listing of all
                Internet and x.25 (HP-UX) network files.
	    An Internet address is specified in the form (Items in square brackets are optional.): >[46][protocol][@hostname|hostaddr][:service|port]
	         46 specifies the IP version, IPv4 or IPv6 that applies to the following address.
             protocol is a protocol name - TCP, UDP
             hostname is an Internet host name.
             hostaddr is a numeric Internet IPv4 address in dot form; or an IPv6 numeric address in colon form, enclosed in brackets
             service is an /etc/services name - e.g., smtp - or a list of them.
             port is a port number, or a list of them.
-n: inhibits the conversion of network numbers to host names for network files.
-p s: excludes or selects  the listing of files for the processes whose optional process IDentification (PID) numbers are in the comma-separated set s - e.g., "123" or "123,^456".
-P: inhibits  the  conversion of port numbers to port names for network files.
