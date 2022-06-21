# Andorid_PDF_Genertion_With_WA_Sharing
This App ask user preference and generate PDF also PDF can be shared to WhastApp


## Code Overview
```
      name = findViewById(R.id.name);
        age = findViewById(R.id.age);
        city = findViewById(R.id.city);
        bio = findViewById(R.id.bio);

        button = findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {

                if (name.getText().toString().isEmpty()) {
                    name.setError("Name is required");
                    return;
                }

                if (age.getText().toString().isEmpty()) {
                    age.setError("Age is required");
                    return;
                }

                if (city.getText().toString().isEmpty()) {
                    city.setError("City is required");
                    return;
                }

                if (bio.getText().toString().isEmpty()) {
                    bio.setError("Bio is required");
                    return;
                }

                generatePDF();
            }
        });
        
        private void generatePDF() {

        String path = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS).toString();

        try {
            File pdfFile = new File(path, "Demo_PDF.pdf");

            if (!pdfFile.exists())
                pdfFile.createNewFile();
            else
                Toast.makeText(this, "PDF already exist, Not creating new one", Toast.LENGTH_SHORT).show();

            Document document = new Document();
            PdfWriter.getInstance(document, new FileOutputStream(pdfFile.getAbsolutePath()));
            document.open();

            //Adding paragraph in the pdf
            Paragraph title_para = new Paragraph();
            String userNameTitle = name.getText().toString();
            title_para.add(userNameTitle);
            title_para.setSpacingAfter(10.0f);
            title_para.setAlignment(Element.ALIGN_CENTER);

            Paragraph des_para = new Paragraph();
            String userBio = bio.getText().toString() + "\n\n";
            des_para.add(userBio);

            document.add(title_para);
            document.add(des_para);

            //Adding Table in the PDF
            float[] width = {50f, 50f, 50f};
            PdfPTable pdfPTable = new PdfPTable(width);

            PdfPCell pdfPCell = new PdfPCell(new Paragraph("Name"));
            pdfPTable.addCell(pdfPCell);
            pdfPTable.addCell(new PdfPCell(new Paragraph("Age")));
            pdfPTable.addCell(new PdfPCell(new Paragraph("City")));

            pdfPTable.addCell(new PdfPCell(new Paragraph(name.getText().toString())));
            pdfPTable.addCell(new PdfPCell(new Paragraph(age.getText().toString())));
            pdfPTable.addCell(new PdfPCell(new Paragraph(city.getText().toString())));

            document.add(pdfPTable);
            document.close();

            Toast.makeText(this, "PDF Generated Successfully\nLocation :: " + pdfFile.getAbsolutePath(), Toast.LENGTH_SHORT).show();

            new AlertDialog.Builder(MainActivity.this)
                    .setTitle("PDF Created")
                    .setMessage("Saved to " + path + "\n\nNamed :: Demo_PDF.pdf")
                    .setCancelable(false)
                    .setIcon(R.drawable.folder)
                    .setPositiveButton("OK", new DialogInterface.OnClickListener() {
                        @Override
                        public void onClick(DialogInterface dialogInterface, int i) {
                            dialogInterface.dismiss();
                        }
                    })
                    .setNeutralButton("Share to Whatsapp", new DialogInterface.OnClickListener() {
                        @Override
                        public void onClick(DialogInterface dialogInterface, int i) {
                            sharePdfToWhatsApp(MainActivity.this);
                        }
                    }).show();

        } catch (IOException | DocumentException e) {
            e.printStackTrace();
        }
    }

    private void sharePdfToWhatsApp(Activity activity) {
        File outputFile = new File(Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS), "Demo_PDF.pdf");
        //Uri uri1 = Uri.fromFile(outputFile);
        Uri uri = FileProvider.getUriForFile(MainActivity.this, BuildConfig.APPLICATION_ID + ".provider", outputFile);

        Intent share = new Intent();
        share.setAction(Intent.ACTION_SEND);
        share.setType("application/pdf");
        share.putExtra(Intent.EXTRA_STREAM, uri);
        share.setPackage("com.whatsapp");
        share.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);
        activity.startActivity(share);
    }
```
