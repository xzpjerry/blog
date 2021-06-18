- a better implementation that scales

```python
import json
import xml.etree.ElementTree as etree
from abc import abstractproperty, abstractstaticmethod

class AbstractConnector:
  	def __init__(self, filepath):
      	pass
  	@abstractstaticmethod
  	def should_use_this_connector(filepath):
    		pass
  	@abstractproperty
  	def parsed_data(self):
    		pass
    @classmethod
    def connection_factory(cls, filepath):
      	for sub in cls.__subclasses__():
          	if sub.should_use_this_connector(filepath):
              	connector = sub(filepath)
                return connector(filepath)
				raise ValueError('Cannot connect to {}'.format(filepath))

class JSONConnector(AbstractConnector):
    def __init__(self, filepath):
        self.data = dict()
        with open(filepath, mode='r', encoding='utf8') as f:
            self.data = json.load(f)
		@staticmethod
  	def should_use_this_connector(filepath):
    		return filepath.endswith('json')
    @property
    def parsed_data(self):
        return self.data

class XMLConnector(AbstractConnector):
    def __init__(self, filepath):
        self.tree = etree.parse(filepath)
  	@staticmethod
  	def should_use_this_connector(filepath):
    		return filepath.endswith('xml')
    @property
    def parsed_data(self):
        return self.tree
```

