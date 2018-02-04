# PIL draw.text中文乱码问题

用`unicode(text, encode)`指定编码格式：
```python
draw.text((0, 0), unicode("This is 周华聪", 'utf-8'), (255, 255, 0), font=font)
```