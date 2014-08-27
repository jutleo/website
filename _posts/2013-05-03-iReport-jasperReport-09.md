---
layout: post
title: "iReport+jasperReport之scriptlet（续）"
tagline: "Series of articles of iReport and JasperReport "
description: "iReport+jasperReport之scriptlet（续）"
category: iReport
tags: iReport、jasperReport
---

写上篇[iReport+jasperReport之scriptlet][scriptlet]时遗漏了一个问题，getParameterValue、setParameterValue方法可以操作Parameter,Field/Variable该怎么set呢？
看看 JRAbstractScriptlet.java类的这个方法：  
	
	public void setData(
        Map parsm,
        Map fldsm,
        Map varsm,
        JRFillGroup[] grps
        )
    {
        parametersMap = parsm;
        fieldsMap = fldsm;
        variablesMap = varsm;
        groups = grps;
    }
	
我们可以通过这个方法把我们期望的数据组装成Map然后set进去，可是要只针对个别字段怎么处理呢，调用此方法似乎不太合常理。
仔细查看API却没有实际能调用的API吧！这个似乎不太合乎，仔细看看确实没有调用的，至少目前我还是没有发现，怎么办 自己写吧！   
**设置Field方法：**  
	
	public void setFieldValue(String fieldName, Object value) throws JRScriptletException
    {
        JRFillField field = (JRFillField)this.fieldsMap.get(fieldName);
        if (field == null)
        {
            throw new JRScriptletException("FieldName not found : " + fieldName);
        }
        
        field.setValue(value);
    }
	
**设置Variable方法：**  
	
	public void setVariableValue(String variableName, Object value) throws JRScriptletException
    {
        JRFillVariable variable = (JRFillVariable)this.variablesMap.get(variableName);
        if (variable == null)
        {
            throw new JRScriptletException("Variable not found : " + variableName);
        }
        
        if (value != null && !variable.getValueClass().isInstance(value) )
        {
            throw new JRScriptletException("Incompatible value assigned to variable " + variableName + ". Expected " + variable.getValueClassName() + ".");
        }
        
        variable.setValue(value);
    }
	
OK！这样我们就可以针对报表上的每一个字段处理了，测试通过 代码就不贴了哦,写上篇的时候忘记这两个方法是我自己加的，查看API时才发现。所以来了个续。  
	

[scriptlet]: http://jutleo.github.io/2013/05/03/iReport-jasperReport-08/