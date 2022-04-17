# read-jpeg-and-png-width-and-height-python
Simple, efficient, lightweight functions to quickly and efficiently read jpeg and png width and height for python 2 and 3. The images are explicitly parsed to the extent that they need to be; if you get values back other than 0,0 you can be very confident they are correct.

Note that I use a tab size of 4, and code is formatted accordingly. Github doesn't allow control of tab size in markdown code blocks, sigh. What they _want_ you to do is convert the tabs to spaces, which is _insane._

# Python 2

```python
# Python 2
# reads jpeg image width and height quickly and efficiently
# returns width,height or 0,0 if cannot determine
def jpeg_res(filename):
	try:	# catch every type of exception
		too = True
		ct = 0
		# open image for reading in binary mode
		with open(filename,'rb') as img:
			x = img.read(2)	# Look for jpeg file type marker
			if ord(x[0]) != 0xff or ord(x[1]) != 0xd8:
				return 0,0	# not a jpeg
			while too:
				ct = ct + 1
				if ct > 100:	# frame block should be pretty early... quit if not
					return 0,0
				x = img.read(2)	# read marker
				l = img.read(2)	# read size (includes size bytes... bizarre, but true)
				if ord(x[0]) == 0xff and ord(x[1]) == 0xc0:
					ll = (ord(l[0]) * 256) + ord(l[1])	# get block size
					z = img.read(ll-2)					# read block, minus size bytes
					height = ord(z[1]) * 256 + ord(z[2])
					width  = ord(z[3]) * 256 + ord(z[4])
					return width,height
				else: # not the right kind of block
					ll = (ord(l[0]) * 256) + ord(l[1])	# get block size
					img.seek(ll-2,1)					# seek relative past the block
	except Exception,e: # you can use str(e) to figure out the problem here
		pass	# something went completely wrong.
	return 0,0	# error return

# python 2 version
# reads png image width and height quickly and efficiently
# returns width, height or 0,0 if cannot determine
def png_res(filename):
	w = 0
	h = 0
	try:
		with open(filename,'rb') as img:
			x = img.read(8) # png signature
			match = [137,80,78,71,13,10,26,10]
			for i in range(0,8):
				if ord(x[i]) != match[i]:
					return w,h
			x = img.read(4) # chunk length
			y = ord(x[0]) * 16777216 + ord(x[1]) * 65536 + ord(x[2]) * 256 + ord(x[3])
			if y != 13:
				return w,h
			x = img.read(4) # chunk name
			if x != 'IHDR':
				return w,h
			x = img.read(4) # image width
			w = ord(x[0]) * 16777216 + ord(x[1]) * 65536 + ord(x[2]) * 256 + ord(x[3])
			x = img.read(4) # image height
			h = ord(x[0]) * 16777216 + ord(x[1]) * 65536 + ord(x[2]) * 256 + ord(x[3])
	except Exception,e: # you can use str(e) to figure out the problem here
		pass
	return w,h

```
# Python 3
```python3
# Python 3
# Read jpeg image width and height quickly and efficiently
# returns width, height or 0,0 if cannot determine
def jpeg_res(filename):
	try:
		too = True
		ct = 0
		# open image for reading in binary mode
		with open(filename,'rb') as img:
			x = img.read(2) # Look for jpeg file type marker
			if x[0] != 0xff or x[1] != 0xd8:
				return 0,0	# not a jpeg
			while too:
				ct = ct + 1
				if ct > 100:	# frame block should be pretty early... quit if not
					return 0,0
				x = img.read(2)	# read marker
				l = img.read(2)	# read size (includes size bytes... bizarre, but true)
				if x[0] == 0xff and x[1] == 0xc0:
					ll = l[0] * 256 + l[1]	# get block size
					z = img.read(ll-2)		# read block, minus size bytes
					height = z[1] * 256 + z[2]
					width  = z[3] * 256 + z[4]
					return width,height
				else: # not the right kind of block
					ll = l[0] * 256 + l[1]	# get block size
					img.seek(ll-2,1)	# seek relative past the block
	except Exception as e:	# you can use str(e) to figure out the problem here
		pass	# something went completely wrong
	return 0,0

# python 3 version
# reads png image width and height quickly and efficiently
# returns width, height or 0,0 if cannot determine
def png_res(filename):
	w = 0
	h = 0
	try:
		with open(filename,'rb') as img:
			x = img.read(8) # png signature
			match = [137,80,78,71,13,10,26,10]
			for i in range(0,8):
				if x[i] != match[i]:
					return w,h
			x = img.read(4) # chunk length
			y = x[0] * 16777216 + x[1] * 65536 + x[2] * 256 + x[3]
			if y != 13:
				return w,h
			x = img.read(4) # chunk name
			if x.decode('ascii') != 'IHDR':
				return w,h
			x = img.read(4) # image width
			w = x[0] * 16777216 + x[1] * 65536 + x[2] * 256 + x[3]
			x = img.read(4) # image height
			h = x[0] * 16777216 + x[1] * 65536 + x[2] * 256 + x[3]
	except Exception as e: # you can use str(e) to figure out the problem here
		pass
	return w,h

```
