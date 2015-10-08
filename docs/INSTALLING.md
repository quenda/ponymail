# Installing Pony Mail #

## Pre-requisites ##
You will need the following software installed on your machine:

- ElasticSearch >= 1.3 (2.0 is not supported yet)
- Python 3.x for the archiver plugin (setup.py will handle dependencies) and importer
- Apache HTTP Server 2.4.x with mod_lua (see http://modlua.org/gs/installing if you need to build mod_lua manually)
- Lua 5.2 or 5.1 with the following modules: cjson, luasec, luasocket


## Download and Install ##
- Download the git repo: `git clone https://github.com/Humbedooh/ponymail.git`
- Start ElasticSearch on the machine it needs to run on.
- Run setup.py in the `tools` dir:
```
      $cd toosl
      $python3.4 setup.py
      ...[follow instructions in the setup script]
```
- Edit `site/js/config.js` to suit your needs (usually very little editing is needed)

### Using Apache HTTP Server: ###
- Set up a VirtualHost block in Apache httpd that points to the `site/` directory in Pony Mail
- Add the configuration snippets from `configs/ponymail_httpd.conf` to the virtual host
- Start Apache httpd to enable the user-facing interface

### Using nginx: ###
- Make sure lua-apr is installed
- Set up a Server block in nginx that points to the `site/` directory in Pony Mail
- Add the configuration snippets from `configs/ponymail_nginx.conf` to the server config
- Start nginx to enable the user-facing interface


## Setting up the archiver ##
If your mailing list supports feeding emails to a program, feed the incoming new emails to `python3.4 /path/to/tools/archiver.py`
and it will use STDIN as the transport mechanism. If you are simply using aliases or dot-forwards and no ML system, you can
add (for example) `"|/usr/bin/python3.4 /path/to/tools/archiver.py"` to your alias file to enable archiving.
If you are not using a Mailing List manager, you will need to tell Pony Mail which email header determines the
list ID using the --altheader argument, for instance:
```
    foolist: "|/usr/bin/python3.4 /path/to/tools/archiver.py --altheader delivered-to"
```

If you are using MailMan 3, you can add archiver.py as an archive by following the instructions inside the python script:
- Copy the archiver.py file to `$mailman_plugin_dir/mailman_ponymail/__init__.py`
- Copy ponymail.cfg to the same dir (for ES configuration)
- Enable the module by adding the following to your `mailman.cfg` file::
```
  [archiver.ponymail]
  # Pony Mail
  class: mailman_ponymail.Archiver
  enable: yes
```

## Public versus private lists ##
In MailMan 3, this should be auto-detected and is not a concern.
When using other ML systems via piping to STDIN, you should add
the --private arg to the python script to mark an email as private:
```
    foolist-private: "|/usr/bin/python3.4 /path/to/tools/archiver.py --private"
    foolist-public: "|/usr/bin/python3.4 /path/to/tools/archiver.py"
```