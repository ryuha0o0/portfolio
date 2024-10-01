---
widget: slider  # Use the Slider widget as this page section
weight: 1  # Position of this section on the page
active: true  # Publish this section?
headless: true  # This file represents a page section.dddd


design:
  # Slide height is automatic unless you force a specific height (e.g. '400px')
  slide_height: '350px'
  slide_width: '100px'
  is_fullscreen: false
  # Automatically transition through slides?
  loop: true
  # Duration of transition between slides (in ms)
  interval: 3000

content:
  slides:
    - title: 👋 Welcome to the group
      content: Take a look at what we're working on...
      align: center
      background:
        image:
          filename: file1.jpg
          filters:
            brightness: 0.4
        position: center
        color: '#000'
    - title: Lunch & Learn ☕️
      content: 'Stick together!'
      align: left
      background:
        image:
          filename: file2.jpg
          filters:
            brightness: 0.4
        position: center
        color: '#000'
    - title: World-Class Semiconductor Lab
      content: 'Just opened last month!'
      align: right
      background:
        image:
          filename: file3.jpg
          filters:
            brightness: 0.4
        position: center
        color: '#000'
      link:
        icon: graduation-cap
        icon_pack: fas
        text: Join Us
        url: ../contact/
---
