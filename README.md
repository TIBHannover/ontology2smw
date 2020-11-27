# Ontology to SMW
_**Automating an RDF ontology import into Semantic Mediawiki**_

[Semantic Mediawiki](https://www.semantic-mediawiki.org)(SMW) allows 
[external ontologies to be imported into a Mediawiki (MW) instance](https://www.semantic-mediawiki.org/wiki/Help:Import_vocabulary). 
External ontology's properties and classes can be used inside the MW instance, and produce a RDF exports of 
the wiki pages, which point to IRIs of the imported ontology(s).

The process of importing is simple, but time consuming. Hence making it a perfect candidate for an automated process, 
which can be run at anytime a new version of the ontology is published, hence this python script.

**In order for the terms of an ontology to be imported to SMW instance they should include the property
`aeon:SMW_datatype` and the appropriate [SMW datatype](https://www.semantic-mediawiki.org/wiki/Help:List_of_datatypes) or Category for Classes**

![ontology2smw import workflow](docs/ontology2smw_aeon.svg?raw=true)

Supports:
* python3.6, python3.7

Does *not yet* support: 
* python3.8 as RDFlib is not yet supposrting Python3.8 (see [issue](https://github.com/RDFLib/rdflib/issues/1190))

## Install:

### Manually
```bash
git clone https://github.com/TIBHannover/ontology2smw.git
cd ontology2smw
# ---- (optional) create and activate a virtual-environment
python -m venv venv
source venv/bin/activate
# ---- virtual environment created
pip install --upgrade setuptools
python setup.py install
```

## Run:

**Run:**

Using local ontology:<br/>`ontology2smw --format ttl --ontology aeon/aeon.ttl`

Using a remote ontology:<br/>`ontology2smw --format ttl --ontology https://raw.githubusercontent.com/tibonto/aeon/master/aeon.ttl`

Writing to wiki pages:<br/>`ontology2smw --format ttl --ontology aeon/aeon.ttl --write` 

Asking for help:<br/>`ontology2smw --help`
```bash
usage: ontology2smw [-h] [-w] [-o ONTOLOGY] [-f {application/rdf+xml,ttl}]

                   ___    
                 { . . } 
--------------o00---'----00o--
confIDent ontology2SMW  
-----------------------------

optional arguments:
  -h, --help            show this help message and exit
  -w, --write           writes the output to wiki or file. Default: False (dry-run).
  -o ONTOLOGY, --ontology ONTOLOGY
                        Ontology file or URI
  -f {application/rdf+xml,ttl}, --format {application/rdf+xml,ttl}
                        Ontology format. Default value: application/rdf+xml
```

<!--

**Requirements:** 

In virtual environment install requirements `pip install -r requirements.txt`

`python setup.py install`


**Create a build:** 

`python setup.py sdist bdist_wheel`
-->

### To write wiki pages
**wikidetails.yml & wiki write access**
* Ensure user your wiki user account belongs to bot group: see wiki page `Special:UserRights`
* Create a bot password in wiki page: `Special:BotPasswords`
* **copy** `wikidetails.template.yml` as `wikidetails.yml` and fill in bot name and password:<br/>
* **give the bot appropriate rights**: basic, editinterface, editpage, editprotected, createeditmovepage, highvolume    

The **local test VM**, mentioned in the following section, **sets a bot account for 
the Admin user**, so that once the VM is created **you can just run the script against it.**
Details for that wiki, VM, bot username and password are set in `wikidetails.yml`

 
    
### Test on local Virtual Machine
In order to test ontology2smw in action in a isolated virtual 
environment, the repository includes a Ansible playbook which creates a VM with Mediawiki installed, and also creates a Bot account for wiki user Admin.

The playbook sets Mediawiki with:
* URL: http://192.168.100.100/w
* SemanticMediawiki extension
* bot account for Admin, with details (in wikidetail.yml):
   * user: Admin 
   * user password: adminpassword
   * bot: Admin@ontology2smwbot
   * bot password: botpasswordbotpasswordbotpassword

Requirements:
* [VirtualBox](https://www.virtualbox.org/)
* [Vagrant](https://www.vagrantup.com/)
* [Ansible](https://www.ansible.com/) 

**Create VM:**
```bash
cd vm
vagrant up
ansible-playbook playbook_mw.yml
```
Once tests are done you can either,
* suspend the VM: `vagrant suspend` so it can be reused from the state it was left in with `vagrant up` 
* destroy it: `vagrant destroy`

    
## Develop

```bash
git clone https://github.com/TIBHannover/ontology2smw.git
cd ontology2smw
# (optional) create and activate a virtual-environment
pip install -r requirements.txt
python -m ontology2smw
```
    
    
## Properties from other ontologies
...

## queries with [ARQ](https://jena.apache.org/documentation/query/)
`arq --data=aeon/aeon.ttl   --query=ontology2smw/queries/ontology.rq`

`arq --data=aeon/aeon.ttl   --query=ontology2smw/queries/query_classes.rq`

`arq --data=aeon/aeon.ttl   --query=ontology2smw/queries/query_properties.rq`


# Other Scripts
## confident2wikidata_mapping.py
Script to make a YAML file that holds the mapping of AEON/ConfIDent properties to Wikidata properties, 
by parsing the annotation properties aeon:WikidataLabel and aeon:WikidatURI from the recent ontology TTL.

    :param  source          The URL of the recent aeon ontology (defaults to AEON/ConfIDent branch)
    :param  outfile_path     The file path of the YAML generated by the script
