---
title: Generating Ticket PDFs in Open Event API Server
layout: post
author: poush
permalink: /generating-ticket-pdfs-in-open-event-api-server/
tags:
- python, flask, fossasia, gsoc, open-event, api server, html, pdf, xhtml2pdf, send tickets
source-id: 1SxFw8p8bBl2YJtoQa9n9lZt5sDiYdwiJ4WSyLu9v53s
published: true
---
![image alt text]({{ site.url }}/public/rvHPVxaNuPA2Zs7PARrg_img_0.png)

Generating Ticket PDFs in Open Event API Server

In the ordering system of [Open Event API Server](https://github.com/fossasia/open-event-orga-server), there is requirement to send email notifications to the attendees. These attendees receives the url of the pdf of generated ticket. On creating the order, first the pdfs are generated and stored in the preferred storage location and then these are sent to the users through the email.

Generating PDF is a simple process, using **[xhtml2pd**f](https://github.com/xhtml2pdf/xhtml2pdf)** **we can generate PDFs from the html. The generated pdf is then passed to storage helpers to store it in the desired location and pdf-url is updated in the attendees record.

### Sample PDF

(just the sample ticket of sample event)

![image alt text]({{ site.url }}/public/rvHPVxaNuPA2Zs7PARrg_img_1.png)

### PDF Template

The templates are written in html which are then converted using the module [xhtml2pdf](https://github.com/xhtml2pdf/xhtml2pdf).

To store the templates a new directory was created at * ***_app/templates _**where all html files are stored. Now, The template directory needs to be updated at flask initializing app so that template engine can pick the templates from there. So in app/__init__.py we updated flask initialization with

template_dir = os.path.dirname(__file__) + "/templates"

app = Flask(__name__, static_folder=static_dir, template_folder=template_dir)

This allows the template engine to pick the templates files from this template directory.

### Generating PDFs

Generating PDF is done by rendering the html template first. This html content is then parsed into the pdf

file = open(dest, "wb")

pisa.CreatePDF(cStringIO.StringIO(pdf_data.encode('utf-8')), file)

file.close()

The generated pdf is stored in the temporary location and then passed to storage helper to upload it.

uploaded_file = UploadedFile(dest, filename)

upload_path = UPLOAD_PATHS['pdf']['ticket_attendee'].format(identifier=get_file_name())

new_file = upload(uploaded_file, upload_path)

This generated pdf path is returned here

### Rendering HTML and storing PDF

for holder in order.ticket_holders:

   if holder.id != current_user.id:

       pdf = create_save_pdf(render_template('/pdf/ticket_attendee.html', order=order, holder=holder))

   else:

       pdf = create_save_pdf(render_template('/pdf/ticket_purchaser.html', order=order))

   holder.pdf_url = pdf

   save_to_db(holder)

The html is rendered using flask template engine and passed to **create_save_pdf** and link is updated on the attendee record.

### Sending PDF on email

These pdfs are sent as a link to the email after creating the order. Thus a ticket is sent to each attendee and a summarized order details with attendees to the purchased.

send_email(

   to=holder.email,

   action=TICKET_PURCHASED_ATTENDEE,

   subject=MAILS[TICKET_PURCHASED_ATTENDEE]['subject'].format(

       event_name=order.event.name,

       invoice_id=order.invoice_number

   ),

   html= MAILS[TICKET_PURCHASED_ATTENDEE]['message'].format(

       pdf_url=holder.pdf_url,

       event_name=order.event.name

   )

)

### References

1. Readme - xhtml2pdf[https://github.com/xhtml2pdf/xhtml2pdf/blob/master/README.rst](https://github.com/xhtml2pdf/xhtml2pdf/blob/master/README.rst)

2. Using xhtml2pdf and create pdfs[https://micropyramid.com/blog/generating-pdf-files-in-python-using-xhtml2pdf/](https://micropyramid.com/blog/generating-pdf-files-in-python-using-xhtml2pdf/)

