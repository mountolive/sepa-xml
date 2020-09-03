# SEPA-XML

This module gives you a wrapper around generating a SEPA XML file. It works by using [Handlebars](http://handlebarsjs.com/) to generate the file instead of using the conventional XML tree approach used by other plugins.

It does, however, run the final result through a validator and will throw an error if there's an issue with it!

This fork can take an `ibanValidator` as a parameter.

## Formats

These can be found in the `formats` folders, and are simple Handlebars files representing the XML schema.

 * `pain.001.001.03`
 * `pain.001.001.02` (thanks [@PierrickP](https://github.com/PierrickP))
 * `pain.008.001.02` Direct Debit Initiation
 * Want to add one? [Send us a pull request](https://github.com/mgmco/sepa-xml/compare/)!

## Usage

```javascript
var SepaXML = require('sepa-xml');
var XMLFile = new SepaXML(); // takes a single argument which is the format, default is 'pain.001.001.03'

// This sets the header data in the file
XMLFile.setHeaderInfo({
  messageId: '123/1',
  initiator: 'XML Corp.'
});

XMLFile.setPaymentInfo({
  id: '123/1',
  method: 'TRF',
  senderName: 'Acme Co.',
  senderIBAN: 'NL39 RABO 0300 0652 64',
  batchBooking: false, // optional (default: false)
  bic: 'RABONL2U'
});

// Add one of these for every transaction
XMLFile.addTransaction({
  id: 'endToEndID',
  amount: 10.00,
  name: 'My Name',
  iban: 'NL39 RABO 0300 0652 64',
  bic: 'RABONL2U',  // optional can be auto-found it
  description: 'My great payment'
});

XMLFile.compile(function (err, out) {
  // your XML data gets output here
});
```
Example pain.008.001.02

```javascript
var SepaXML = require('sepa-xml');
var XMLFile = new SepaXML({ format: 'pain.008.001.02' });
// You could use new
// SepaXML({ format: 'pain.008.001.02', ibanValidator: { isValid: iban => true } })
// ibanValidator must implement isValid

// This sets the header data in the file
XMLFile.setHeaderInfo({
  messageId: '123/1',
  initiator: 'XML Corp.'
});

XMLFile.setPaymentInfo({
  when: new Date('2020-02-01'),
  id: 'RCUR 123/1',
  method: 'DD',
  creditorName: 'XML Corp',
  creditorIBAN: 'NL39RABO0300065264',
  creditorIdentifier : 'NL63ZZZ400123790000',
  batchBooking: false, // optional (default: false)
  bic: 'RABONL2U'
});

// Add one of these for every transaction
XMLFile.addTransaction({
  id: '123/1.01',
  amount: '86.00',
  mandateId : 'Mndt-135-1-11-2009',
  mandateSignatureDate : '2009-11-01', 
  name: 'Debitor Name',
  iban: 'NL39RABO0300098765',
  bic: 'RABONL2U',  // optional can be auto-found it
  description: 'Annual Fee 2020'
});

XMLFile.compile(function (err, out) {
  // your XML data gets output here
});
```
## Contributing

 * Would be nice if someone wrote more templates for some of the other formats used for SEPA.
 * The BICS lookup list is probably not complete - it would be great if someone could add that data into [the map](https://github.com/mgmco/sepa-xml/blob/master/bics/list.js)
 * Anything else you can think of
