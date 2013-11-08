Fileupload
==========
foreach (var file in files)
			{
				if ( file != null)
				{
					try{
					ParseQuery<ParseObject> query = ParseObject.GetQuery("userPosts");
					ParseObject up = await query.GetAsync(id);
					
					//Startar att 
					Bitmap result = new Bitmap(300, 300); 
					Image image = new Bitmap(file.InputStream);
					//use a graphics object to draw the resized image into the bitmap 
					using (Graphics graphics = Graphics.FromImage(result))
					{
						//set the resize quality modes to high quality 
						graphics.CompositingQuality = System.Drawing.Drawing2D.CompositingQuality.HighQuality;
						graphics.InterpolationMode = System.Drawing.Drawing2D.InterpolationMode.HighQualityBicubic;
						graphics.SmoothingMode = System.Drawing.Drawing2D.SmoothingMode.HighQuality;
						//draw the image into the target bitmap 
						graphics.DrawImage(image, 0, 0, result.Width, result.Height);

					}
					var fileName = Path.GetFileName(file.FileName);
					var path = Path.Combine(Server.MapPath("~/App_Data/uploads"), fileName);
					if (image.Size.Height <= 300 && image.Size.Width <= 300)
					{
						image.Save(path);
					}
					else{
						result.Save(path);
					}

					ParseObject userposts = new ParseObject("userPostsPictures");
					var webClient = new WebClient();
					byte[] data = webClient.DownloadData(path);
					var newFile = new ParseFile(fileName, data);
					await newFile.SaveAsync();

					userposts["expPic"] = newFile;
					userposts["userposts"] = up;
					await userposts.SaveAsync();
						}
					catch{}

				}
			}
			return this.RedirectToAction("MyProfile", "View");
		}
