# Go_webApi_project
A Go project demonstrating the functionality of connecting to web services (webApi)to retrieve data  using a unique access key .
## Fetching Data from Web Services in Go

To connect to a web API, you can use the Get() function from the net/http pack- age in Go. The Get() function accepts a URL as its argument and returns two results:
» A Response struct 
» An error

If there is no error in connecting to the server, the error will be nil, and you can use the ReadAll() function from the io/iotil package to read the response from the server. 

The ReadAll() function returns two results:

» A slice of bytes representing the response from the server
 » An error


## Writing a Go program to connect toawebAPI

Let’s write a simple Go program that connects to the Fixer web API. Fixer is a foreign exchange rates and currency conversion JSON API. Using it, you can pro- grammatically fetch the exchange rates of various currencies. 

after ruuning this code below

package main

import (
	"io"
	"log"

	"fmt"
	"net/http"
)

func main() {

	url :=

		"http://data.fixer.io/api/latest?access_key=<access_key>"

	if resp, err := http.Get(url); err == nil {

		defer resp.Body.Close()
		if body, err := io.ReadAll(resp.Body); err == nil {
			fmt.Println(string(body))
		} else {
			log.Fatal(err)
		}
	} else {
		log.Fatal(err)
	}
	fmt.Println("Done")
}

make sure you insert your access_key given to you by fixer.api.com

once you run the go run main.go command you get the folowing output below

{"success":true,"timestamp":1693960023,"base":"EUR","date":"2023-09-06","rates":{"AED":3.937938,"AFN":78.798047,"ALL":107.732653,"AMD":414.342945,"ANG":1.935188,"AOA":886.648801,"ARS":375.216165,"AUD":1.682063,"AWG":1.931009,"AZN":1.815685,"BAM":1.955503,"BBD":2.168101,"BDT":117.852123,"BGN":1.954864,"BHD":0.404116,"BIF":3039.695612,"BMD":1.072127,"BND":1.462305,"BOB":7.419874,"BRL":5.32654,"BSD":1.073837,"BTC":4.1536723e-5,"BTN":89.206516,}}
Done

You can sign up for a free Fixer plan by going to https://fixer.io/ and clicking the Sign Up Free button,

now go to https://jsonlint.com/

to valdate the above json result 
{
	"success": true,
	"timestamp": 1693960023,
	"base": "EUR",
	"date": "2023-09-06",
	"rates": {
		"AED": 3.937938,
		"AFN": 78.798047,
		"ALL": 107.732653,
		"AMD": 414.342945,
		"ANG": 1.935188,
		"AOA": 886.648801,
        ------------
	}
}

## Unmarshalling of results

When the structure of the result of the API is established, you can now try to unmarshall (decode) the JSON result to your own defined structure. You can define two structs — Result and Error — and unmarshall the result to these two structs:

When you run the below codes with struct and insert your access_key from fixer.com

package main

import (
 "encoding/json"
"fmt" 
"io" 
"log" 
"net/http"
)



type Result struct {

Success bool
Timestamp int
Base string
Date string
Rates map[string]float64




}


type Error struct {
	
Success bool
Error struct { 
Code int
Type string
Info string }
}

func main() {

	url :=

		"http://data.fixer.io/api/latest?access_key=<access-key"

		if resp, err := http.Get(url); err == nil {
			defer resp.Body.Close()
			if body, err := io.ReadAll(resp.Body); err == nil {
			var result Result 
			json.Unmarshal([]byte(body), &result) 
			   if result.Success {
			   for i, v := range result.Rates { 
				fmt.Println(i, v)
			   }
			   } else {
				var err Error
				json.Unmarshal([]byte(body), &err)
				fmt.Println(err.Error.Info) 
			}
		    } else { 
					log.Fatal(err)
				}
		} else {
				log.Fatal(err) 
			}
			fmt.Println("Done") 
		}
				
		A snippet of the output goes:	

PEN 3.950207
SCR 13.858237
BOB 7.423812
CNY 7.839155
HNL 26.753322
KWD 0.330916
BZD 2.166267
GNF 9224.116607
KRW 1430.45665
MMK 2256.785979



## Arranging result in alphabetical order

Notice that when you print out the currencies, they aren’t listed in alphabetical order. To print them in alphabetical order, you need to obtain all the keys (curren- cies) and sort them in alphabetical order. After the currencies are sorted, you can then use them to print out the rates:

package main

import (
	"encoding/json"
	"fmt"
	"io"
	"net/http"
	"sort"
)

type Result struct {
	Success   bool
	Timestamp int
	Base      string
	Date      string
	Rates     map[string]float64
}

type Error struct {
	Success bool
	Error   struct {
		Code int
		Type string
		Info string
	}
}

func main() {

	url :=

		"http://data.fixer.io/api/latest?access_key=<access-key>"

	if resp, err := http.Get(url); err == nil {
		defer resp.Body.Close()
		if body, err := io.ReadAll(resp.Body); err == nil {
			var result Result
			json.Unmarshal([]byte(body), &result)
			if result.Success {

				// create an array to store all keys
				keys := make([]string, 0,
					len(result.Rates))
				// get all the keys---
				for k := range result.Rates {
					keys = append(keys, k)
				}
				// sort the keys
				sort.Strings(keys)
				// print the keys and values in
				// alphabetical order
				for _, k := range keys {
					fmt.Println(k, result.Rates[k])
				}
				/*
						for i, v := range result.Rates {
							fmt.Println(i, v)
					}
				*/
			} else {
				var err Error
				json.Unmarshal([]byte(body), &err)
				fmt.Println(err.Error.Info)
			}
		}
	}
}



## The resultants outputs snippets

AED 3.940078
AFN 78.86426
ALL 109.17489
AMD 414.567358
ANG 1.936824
AOA 888.201835
ARS 375.42282
AUD 1.679518
AWG 1.932054
AZN 1.810056

...
ZMK 10601.434336
ZMW 23.992715
ZWL 379.245091



## Refactoring the code for decoding JSON data







