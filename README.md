import React, { useState } from "react";
import { Tabs, Tab, Box } from "@mui/material";
import { motion } from "framer-motion";

const tabsData = ["Tab 1", "Tab 2", "Tab 3", "Tab 4"];

const TabComponent = () => {
  const [currentIndex, setCurrentIndex] = useState(0);
  const [prevIndex, setPrevIndex] = useState(0);

  const handleChange = (event, newIndex) => {
    setPrevIndex(currentIndex);
    setCurrentIndex(newIndex);
  };

  // Determine direction of slide animation
  const direction = currentIndex > prevIndex ? 1 : -1;

  return (
    <Box sx={{ width: "100%" }}>
      <Tabs value={currentIndex} onChange={handleChange}>
        {tabsData.map((tab, index) => (
          <Tab key={index} label={tab} />
        ))}
      </Tabs>

      <Box sx={{ position: "relative", overflow: "hidden", height: "200px" }}>
        <motion.div
          key={currentIndex}
          initial={{ x: direction * 100 + "%", opacity: 0 }}
          animate={{ x: "0%", opacity: 1 }}
          exit={{ x: direction * -100 + "%", opacity: 0 }}
          transition={{ type: "tween", duration: 0.5 }}
          style={{
            position: "absolute",
            width: "100%",
            height: "100%",
          }}
        >
          <Box
            sx={{
              padding: 2,
              background: "#f0f0f0",
              display: "flex",
              justifyContent: "center",
              alignItems: "center",
              height: "100%",
            }}
          >
            {tabsData[currentIndex]} Content
          </Box>
        </motion.div>
      </Box>
    </Box>
  );
};

export default TabComponent;
