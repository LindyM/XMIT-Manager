#XMIT Manager for z/OS
=====================

ISPF dialog that lists, creates, views, and extracts TSO TRANSMIT
(XMIT) data sets.

Create and extract call the system TRANSMIT and RECEIVE commands.
View is pure REXX. It reads the card images, reassembles segments,
and decodes INMR text units. It does not send the file anywhere.

Libraries, prefixed by your TSO userid
  userid.XMITMGR.EXEC      REXX
  userid.XMITMGR.PANELS    ISPF panels
  userid.XMITMGR.MSGS      ISPF messages

Override the panel and message library names in Options. They are
saved in the ISPF profile as XMTPLIB and XMTMLIB.

Install
  1. Submit JCL(XMITINST). It allocates the three libraries.
     Or from TSO run EXEC(XMITALC) after that member is available.
  2. Transfer each member in this package into the matching
     library. Use text mode so ASCII is converted to EBCDIC.
     RECFM=FB, LRECL=80. Do not send the members in binary.
     Every line in this package is within 80 bytes.
  3. The EXEC library must be on SYSEXEC or SYSPROC, or start
     the dialog with:
       EXEC 'userid.XMITMGR.EXEC(XMITMGR)'
     From ISPF option 6 you can then enter:
       XMITMGR

Options
  1  List      LISTCAT a level, or the members of a PDS of XMITs.
               Line commands: V view, E extract, T text, H hex,
               D delete.
  2  Create    TRANSMIT node.userid DATASET(source)
               OUTDATASET(xmit) NOLOG NONOTIFY
               Fill in Members as MEM1,MEM2 when you do not want
               the whole PDS. SEQ or PDS forces that operand.
               Edit = YES opens ISPF EDIT on the source first.
  3  View      Origin, target, time, INMR02 attributes, IEBCOPY
               directory names and ISPF stats, text, and hex.
               Commands on the view panel: C M T H E.
  4  Extract   RECEIVE INDATASET. A member name receives the
               whole transmission to a temporary data set, then
               copies that member with LMCOPY.
  5  Options   Default node, userid, replace, and libraries.

Commands on panels: END, RETURN, HELP, and the list commands
FIND text, REFRESH, and CREATE. PF1 opens panel XMITHELP.
PF3 is END.

Batch
  JCL(XMITSEND) writes an XMIT with TRANSMIT OUTDATASET.
  JCL(XMITRECV) restores one with RECEIVE. The reply to the
  RECEIVE prompt is the next SYSTSIN record.

View limits
  An IEBCOPY unload is not reloaded by View. Member names and
  ISPF statistics come from the directory blocks. Text found in
  the unload is a best-effort scan of fixed-length lines.
  Use Extract when you need an exact data set.
  View reads the XMIT with EXECIO and stops after 80000 records.
  Use Extract for very large transmissions.

Authority
  TRANSMIT and RECEIVE must be permitted for your TSO user.
  View only needs read access to the XMIT data set.
  Delete issues TSO DELETE after you reply YES on the confirm
  panel.

Direct start
  XMITMGR VIEW 'hlq.FILE.XMIT'
  XMITMGR EXTRACT 'hlq.FILE.XMIT'
