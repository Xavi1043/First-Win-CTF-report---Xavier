# First-Win-CTF-report---Xavier
https://blueteamlabs.online/home/challenge/the-planets-prestige-e5beb8e545

Phishing Email Investigation — "A Hope to CoCanDa"


SOC analysis of a spoofed phishing email and its disguised attachment chain.
Defanged indicators are written with [.] and hxxp to prevent accidental navigation.




Investigation Summary

An Army Major received a phishing email purporting to be from the abductor of the
CoCanDa president's daughter, demanding a ransom of 1 billion CoCanDs. Analysis of
the email headers confirmed the sender was spoofed (SPF fail, mismatched
Reply-To). The attached "puzzle" file was disguised as a PDF but was in fact a
ZIP archive containing three further files, one of which concealed an encoded
location for the ransom exchange. Metadata analysis tied the attachment back to the
attacker's reply-to identity.

No endpoint or network telemetry was in scope; analysis was limited to the email
and its attachments. No payload execution or detonation was performed, so malware
classification and any command-and-control activity are reported as suspected /
probable only.


Findings

FieldValueTimeMon, 25 Jan 2021 22:41:18 -0800 (PST)  ·  (= 2021-01-26 06:41:18 UTC)HostN/A — no endpoint/network telemetry in scopeSpoofed sender domainmicroapple[.]com — forged From; SPF returned failReply-to / contact domainpashter[.]com — attacker contact (negeja3921@pashter[.]com)Probable C2 domain<FILL IN — recovered from Money.xlsx> — probable C2 (no observed callback; no payload analysis)Origin sending IP93.99.104.210 — originating mail server (resolves to emkei[.]cz, a free anonymous mailer)Possible Malware FamilySuspected malicious attachment (unconfirmed) — no execution/payload analysis performed

File Artifacts (SHA-256)

FileRoleSHA-256PuzzleToCoCanDa.zipDelivered attachment (claimed .pdf, actually ZIP)677F41150FCFC0BEE3589BF08302ABDFD0ACD1268F7A08C4A5FEECFEB48E5B14Money.xlsxEmbedded artifact (contained probable C2 indicator)59D993FCA50A7FE32D9D0271FA4850D63121411661A3A131B3B0809677D898ABGoodJobMajor.pdfEmbedded artifact (ransom proof; author metadata)FF61467A025529020B55685DF1102B44E28310AA79CD3EF2EA6A93AF9477480BDaughtersCrown.jpgEmbedded artifact (image of a crown)B2A4346FAC4B23C85B8E103655DFDEFC4384586AA123F0E60D447E976EA1475F


The PuzzleToCoCanDa.zip hash was computed from the attachment as delivered in
the source .eml, which is the authoritative (original) version of the artifact.




Who / What / When / Where / Why / How


Who — Spoofed sender billjobs@microapple[.]com; replies directed to
negeja3921@pashter[.]com. The PDF author metadata ("Pestero Negeja") corresponds
to the same reply-to account, linking the attachment to the sender.
What — A ransom-themed phishing email carrying a disguised ZIP attachment whose
contents concealed an encoded ransom-exchange location.
When — Received 2021-01-26 06:41:18 UTC (receiver-stamped, mx.google.com).
Where — Email-space only; no internal host or network activity was in scope.
Why — Presumed financially motivated extortion (ransom demand). Intent beyond
the message content is not confirmed.
How — A spoofed email delivered an attachment disguised as a PDF; the recipient
would extract and open it to reach the embedded files and ransom instructions.



Investigation

On 2021-01-25 22:41:18 PST, an Army Major on Earth received an email from the
suspected abductor of the CoCanDa president's daughter. The sender was
billjobs@microapple[.]com and the subject line read "A Hope to CoCanDa." The
message was a ransom note demanding 1 billion CoCanDs in exchange for the safe return
of the president's daughter.

When the email was opened in Notepad++, several red flags were immediately apparent.
The From and Reply-To headers did not match: the message claimed to be from
billjobs@microapple[.]com, but replies were directed to negeja3921@pashter[.]com.
The Authentication-Results header also showed SPF returned a value of fail,
meaning Google determined that the sending IP, 93.99.104.210, was not authorized to
send mail on behalf of microapple[.]com — confirming the sender was spoofed.

The email referenced a "puzzle" attachment, which I investigated next. Using
CyberChef, I decoded both the message body and the attachment, both of which were
Base64-encoded. The attachment was declared to be a PDF, but after converting the
decoded data to hex and checking its magic number, I determined it was actually a
ZIP archive. I renamed the file accordingly and extracted it, which produced three
files.

I ran each extracted file through a hex editor to read its magic number and identify
its true type against a file-signature reference, then corrected each extension. This
left DaughtersCrown.jpg, GoodJobMajor.pdf, and Money.xlsx. DaughtersCrown.jpg
was a picture of a crown; GoodJobMajor.pdf referenced that image as proof of the
kidnapping; and Money.xlsx was a spreadsheet. In the spreadsheet, the abductor
described a location on the first sheet without giving exact details. I cleared the
cell formatting on both sheets to check for hidden content and found an additional
block of encoded text on the second sheet. Running that text through CyberChef
revealed the location for the ransom exchange.

Finally, to find further details on the abductor, I used ExifTool to extract residual
metadata from the files. The Author field of GoodJobMajor.pdf contained the name
"Pestero Negeja," which corresponds to the same account used in the Reply-To header
— tightening the link between the attachment and the sender.

