
Apache Zeppelin, as of now, unfortunately, it does not support export of notebooks. Chances are you would need this feature to maybe share your notebooks with your colleagues, migrate from one machine to another or even to put your work under source version control.

Luckily under the hood saving and importing new notebooks is quite transparent, you can do this with just a few commands. So, without further ado, let's see how exactly can we do a manual export.

Notebooks are located in folders with random names in the Zeppelin notebook directory.

    ls /opt/zeppelin/notebook/  
    2A94M5J1Y  2A94M5J1Z  2AZU1YEZE  2B3D826UD  

The whole notebooks' definition is stored in a single file called note.json (including the source code).

    ls /opt/zeppelin/notebook/2A94M5J1Y/note.json  

In order to export it to another machine, just copy the folder into another Zeppelin installation directory.

There is just one more thing you need to do though. Open the interpreter configuration file:

    vim /opt/zeppelin/conf/interpreter.json  

you will see a section at the end called interpreterBindings. Add a new section with the ID of your imported notebook (should be the same as the folder name, otherwise check note.json file) and associate it with existing interpreters IDs.

    "interpreterBindings": {
        "2B3D826UD": [
          "2AZN2E1JE",
          ...
          "2B219R99U",
          "2B46SWGGN"
        ],
        "2A94M5J1Y": [   <---- imported notebook
          "2AZN2E1JE",
          ...
          "2B219R99U",
          "2B46SWGGN"
        ]

Now restart zeppelin:

    /opt/zeppelin/bin/zeppelin-daemon.sh stop
    /opt/zeppelin/bin/zeppelin-daemon.sh start

That's it. You should now be able to access and run your imported notebook!


> Written with [StackEdit](https://stackedit.io/).
