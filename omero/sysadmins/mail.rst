OMERO.mail
==========

The OMERO server has the ability to send email to any users who have a
properly configured email address. OMERO system administrators can then use
the :program:`omero admin email` command to contact those users.

In order to activate the subsystem, minimally the
:property:`omero.mail.config` property will need to be activated.
It is likely you will need to change the defaults for the following connection properties:

* :property:`omero.mail.host`
* :property:`omero.mail.port`
* :property:`omero.mail.smtp.auth`
* :property:`omero.mail.smtp.starttls.enable`
* :property:`omero.mail.from`

All properties can be found under the :ref:`mail_configuration` section of
:doc:`config`.

.. note:: A current limitation of the system is that emails are not in a queue
    and therefore if you log out or otherwise lose your OMERO session
    before the server has finished sending, the action will abort without
    completing.


Example secure SMTP configurations
----------------------------------

Replace ``omero@gmail.com`` and ``mypassword`` with your real credentials.

Send email via GMail using TLS (port 587):

.. code-block:: properties
    :emphasize-lines: 2,6,7

    omero.mail.config=true
    omero.mail.from=omero@gmail.com
    omero.mail.host=smtp.googlemail.com
    omero.mail.port=587
    omero.mail.smtp.auth=true
    omero.mail.username=omero@gmail.com
    omero.mail.password=mypassword
    omero.mail.smtp.starttls.enable=true

Send email via GMail using SSL (port 465):

.. code-block:: properties
    :emphasize-lines: 2,6,7

    omero.mail.config=true
    omero.mail.from=omero@gmail.com
    omero.mail.host=smtp.googlemail.com
    omero.mail.port=465
    omero.mail.smtp.auth=true
    omero.mail.username=omero@gmail.com
    omero.mail.password=mypassword
    omero.mail.smtp.socketFactory.class=javax.net.ssl.SSLSocketFactory


Example minimum configuration
-----------------------------

::

    $ omero config set omero.mail.config true

By default, this will use ``localhost`` as the mail server on port 25 and send
as the user ``omero``.

To use your actual mail server::

    $ omero config set omero.mail.host smtp.university.example

If authentication is required, then also configure::

    $ omero config set omero.mail.username USER
    $ omero config set omero.mail.password PASS

Setting email addresses
-----------------------

For any user to receive email, a valid email address must be configured.
By default, the `root` OMERO user will *not* have an email address configured.
This can be done from one of the UIs or via the :program:`omero obj` command::

    $ omero obj update Experimenter:0 email=root@university.example

.. note:: Using a mailing list or an alias for the `root` user can simplify
    configuration.

Enabling mail notifications
---------------------------

A number of "mail senders" are available for sending notifications of certain
events on the server. Those available include:

- ``ServerUpMailSender`` and ``ServerDownMailSender`` which mail when the server goes up or down
- ``FailedLoginMailSender`` which can be configured to send for particular users if a bad password is used
- ``ObjectMailSender`` which can be configured to send an email under various conditions. Instances which are configured include:
   - ``newUserMailSender`` which sends an email every time a user is created
   - ``newCommentMailSender`` which sends an email every time a user's image is commented on by another user

To activate the senders, the :file:`etc/blitz/mail-senders.example` can be
copied to a file ending with ".xml".

OMERO.web error reporting
-------------------------

OMERO.web will email the users listed in the :property:`omero.web.admins`
whenever the application identify broken link (HTTP status code 404) or raises
an unhandled exception that results in an internal server error (HTTP status
code 500). This gives the administrators immediate notification of any errors.
The :property:`omero.web.admins` will get a description of the error,
a complete Python traceback, and details about the HTTP request that caused
the error.

.. note:: Reporting errors requires property :property:`omero.web.debug` set
    to ``False`` and works together with :ref:`omeroweb_error_handling`.


Further configuration
---------------------

Finally, if the above mail configuration properties do not cover your needs,
you can add your own implementation as described under |ExtendingOmero|. The
related property is :property:`omero.mail.bean`::

    $ omero config set omero.mail.bean myMailImplementation
