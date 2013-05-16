[![Nodejitsu Deploy Status Badges](https://webhooks.nodejitsu.com/nodejitsu/handbook.png)](https://webops.nodejitsu.com#nodejitsu/webhooks) [![Build Status](https://travis-ci.org/tobie/specref.png?branch=master)](https://travis-ci.org/tobie/specref)

# Web Specifications Reference Server

## API

The API to the service is very simple. It supports two operations, each of which takes the same two parameters. CORS is enabled for all origins. 

The operations are: 

    GET http://webref.herokuapp.com/bibrefs

Used to get a set of bibliographic references. 

    GET http://webref.herokuapp.com/xrefs

Used to get a set of definition cross-references. 

The parameters, to be used in the query string, are: 

    refs=comma-separated list of reference IDs

This is the desired list of reference IDs separated by commas (with no spaces). 

    callback=name of the callback function

By default the service returns JSON data, which is great but not convenient for browsers that do not support CORS yet. For those, simply adding the `callback` parameter with the name of the callback function you want will switch the response to JSON-P. 

Some examples should help: 

    // get references for SVG, REX, and DAHUT
    GET http://webref.herokuapp.com/bibrefs?refs=SVG,REX,DAHUT
    // the same as JSON-P
    GET http://webref.herokuapp.com/bibrefs?refs=SVG,REX,DAHUT&callback=yourFunctionName
    // get cross-references for the CSS Object Model and File API specifications
    GET http://webref.herokuapp.com/xrefs?refs=cssom,fileapi
    // the same as JSON-P
    GET http://webref.herokuapp.com/xrefs?refs=cssom,fileapi&callback=yourFunctionName
            

If you need to find a reference ID (for either bibliographic or cross-references) you need to either lift it from an existing specification, or to find it in the source database. Where to get the latter is explained below. Please note that the identifiers for bibliographic references are not the same as for definition cross-references, and that just because a specification is featured in one does not mean it is also in the other. (Historically, those were two separate databased that were merged. Or, if you really insist on accuracy, the CSS bibref DB was converted into the ReSpec JS DB; the latter was extensively extended and edited, forked into the Specifiction database which was edited, then into the ReSpec v3 database which was also edited, then much of those were merged; in a parallel universe the Anolis bibliographical and cross-reference databases were developed; then all of these were merged into this service. So stop whining and delight in the consistency that you do have.) 

## Updating & Adding

You can make modifications to the databases simply by editing either `bibrefs.json` or `xrefs.json` in the GitHub repository at , using the format described in the previous section. 

In order to do so you can fork the project and make a pull request to update it, or you can ask to be added as a project collaborator (we're pretty open about that) in which case you'll be able to push changes directly (if you promise to be careful about conflicts and double-checking your JSON syntax). 

Whenever a change is pushed to that repository it automatically gets synchronised to the service's database within a few seconds. 

Some rules to observe when editing the databases follow. 

For both DBs: 

*   Before committing, make sure that your JSON is well-formed. Syntax-check it with a tool or some such. Broken JSON means it stops being synchronised to the DB, thereby annoying everyone, and it can even cause the service to stop functioning. Remember: we know unsavoury characters world-wide, and we can find out where you live. 
*   Don't remove entries unless you are 100% certain that no one is using it. Typically that only applies to cases in which you have just added a reference and want to remove it. This applies even if you find a duplicate entry — there are a few, such is life. 
*   Don't duplicate entries. Make treble-sure that what you want to add is not in the DB. Certainly don't add a duplicate entry just because you don't like the reference short name. 

For the cross-references DB: 

*   Entries in this database are typically automatically generated from a specification. If you find yourself hand-editing something here it is quite possible that you may be doing something wrong. Get in touch and we'll figure it out. 

For the bibliographical references DB: 

*   Please avoid using the `html` key as much as you possibly can. Only use it when relying on the split-out fields will simply not get close to accomplishing the reference you need. 
*   When you want to update an existing reference, if you see that it uses the `html` style, please convert it to the split out variant (and delete the `html`). 
*   References in this database are expected to be to the “latest and greatest” version of a given specification. In some cases this may be the draft residing in the editor's repository, or it may be the latest snapshot as published by a Working Group into TR — this choice is left to your appreciation. If you really, *really* want to have a reference to a dated version, then use an ID matching `REFID-YYYYMMDD`. 
*   Keep the entries in alphabetical order. Try to indent them in roughly the same manner that others are.