---
title: 'Projects'
date: 2024-05-19
type: landing

design:
  # Section spacing
  spacing: '5rem'

# Page sections
sections:
  - block: collection
    title: Selected Projects
    text: I enjoy making things. Here are a selection of projects that I have worked on over the years.
    filters:
      folders:
        - project
    design:
      view: article-grid
      fill_image: false
      columns: 3

  # 서브탭 섹션 추가
  - block: tab
    title: Project Categories
    design:
      tabs:
        - title: Web Development
          content: |
            Web development projects I've worked on include building websites, APIs, and full-stack applications.
            Example: [GitHub Repo](https://github.com/example)

        - title: Data Science
          content: |
            Data Science projects I've worked on involve machine learning, data visualization, and analytics.
            Example: [GitHub Repo](https://github.com/example)

        - title: Mobile Apps
          content: |
            Mobile app projects include cross-platform and native app development for both iOS and Android.
            Example: [GitHub Repo](https://github.com/example)
---
