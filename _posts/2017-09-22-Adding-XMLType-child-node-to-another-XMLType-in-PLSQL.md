---
layout: post
title:  "Adding XMLType child node to another XMLType in PL/SQL"
date:   2017-09-22 09:00:00
---

Adding a child node to an XMLType in PL/SQL is a lot fiddlier than I expected. Here I am documenting the solution I came up with so I can find it again.

### Merge XMLType

```sql
----------------------------------------------------------------------------
FUNCTION fw_xml_append_child(p_parent IN XMLTYPE, p_child IN XMLTYPE)
----------------------------------------------------------------------------
  RETURN XMLTYPE
AS
  l_parent_dom DBMS_XMLDOM.DOMDocument;
  l_parent_elem DBMS_XMLDOM.DOMElement;

  l_child_dom DBMS_XMLDOM.DOMDocument;
  l_child_elem DBMS_XMLDOM.DOMElement;
  l_child_node DBMS_XMLDOM.DOMNode;

  l_import_node DBMS_XMLDOM.DOMNode;
  l_result_node DBMS_XMLDOM.DOMNODE;
        
  l_return XMLTYPE;
BEGIN
  l_parent_dom := DBMS_XMLDOM.NEWDOMDOCUMENT(p_parent);
  l_parent_elem := DBMS_XMLDOM.GETDOCUMENTELEMENT(l_parent_dom);

  l_child_dom := DBMS_XMLDOM.NEWDOMDOCUMENT(p_child);
  l_child_elem := DBMS_XMLDOM.GETDOCUMENTELEMENT(l_child_dom);
  l_child_node := DBMS_XMLDOM.MAKENODE(l_child_elem);

  l_import_node := DBMS_XMLDOM.IMPORTNODE(l_parent_dom, l_child_node, TRUE);
  l_result_node := DBMS_XMLDOM.APPENDCHILD(DBMS_XMLDOM.MAKENODE(l_parent_elem), l_import_node);

  l_return := DBMS_XMLDOM.GETXMLTYPE(l_parent_dom);

  DBMS_XMLDOM.FREEDOCUMENT(l_parent_dom);
  DBMS_XMLDOM.FREEDOCUMENT(l_child_dom);
        
  RETURN l_return;
END;
```
